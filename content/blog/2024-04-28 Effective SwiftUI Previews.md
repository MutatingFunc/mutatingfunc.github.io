---
title: "Effective SwiftUI Previews"
date: 2024-04-28T21:00:00-00:00
draft: false
tags: ["Swift"]
categories: ["Coding"]
---

Previews are a handy tool for getting a live look at all possible states of a view, and I often finding myself creating loads without thinking.

Yet I find people often have trouble getting previews to work nicely. This is my attempt to break down my approach for those people. If you've never heard of app 'architecture' or a 'view model', feel free to skip to the end.

If you use TCA, which ties architecture very deeply to the view, my best advice is to look to their docs on the topic.

## The standard approach

The standard approach of using a view model, no protocols, looks a little like the following:

```swift

import SwiftUI

// View-agnostic model

struct WhoppingGreatAPIResponse: Decodable {
    var countFoos: Int
    // Some hard-to-construct additional data
}

actor APIHandler {
    func makeRequest() async {}
}

// View Model

@Observable
class ContentViewModel {
    var thingA: Int
    var thingB: String
    private var apiResponse: WhoppingGreatAPIResponse
    private var apiHandler: APIHandler
    init(thingA: Int, thingB: String, apiResponse: WhoppingGreatAPIResponse, apiHandler: APIHandler) {
        self.thingA = thingA
        self.thingB = thingB
        self.apiResponse = apiResponse
        self.apiHandler = apiHandler
    }
    
    var outputText: String {
        if apiResponse.countFoos < 0 {
            "Hello world! Your number is \(thingA * 123) and your string is \(thingB)"
        } else {
            "Negative foos shouldn't happen"
        }
    }
    
    func submitRequest() async {
        await apiHandler.makeRequest()
    }
}

// View

struct ContentView: View {
    @Bindable var viewModel: ContentViewModel
    
    var body: some View {
        VStack {
            Text(viewModel.outputText)
            Button("Do thing") {
                Task {
                    await viewModel.submitRequest()
                }
            }
        }
    }
}

// Previews
 
#Preview("Success") {
    ContentView(viewModel: ContentViewModel(thingA: 1, thingB: "2", apiResponse: WhoppingGreatAPIResponse(countFoos: 123), apiHandler: APIHandler()))
}

#Preview("Failure") {
    ContentView(viewModel: ContentViewModel(thingA: 1, thingB: "2", apiResponse: WhoppingGreatAPIResponse(countFoos: -123), apiHandler: APIHandler()))
}

// Entry point for app, single page showcase app, in-app showcase listing, etc

struct DataView: View {
    @Bindable var viewModel: ContentViewModel
    
    var body: some View {
        ContentView(viewModel: viewModel)
    }
}

```

With this approach, previews are clearly no good. The view model (be it traditional, or an alternative approach like TCA's concept of a view store + reducer) is often in charge of a great deal of backend logic, as well as mapping data from a third-party format to one your view is interested in. To construct your preview, you have to build all the third-party data which your live view would consume.

Let's break down the pain points:

* Difficulty in building the view for previews.
* Difficulty in distinguishing different preview configurations through code alone.
* Difficulty in reusing logic between previews, never mind constructing showcases like single-page mini-apps, or in-app showcase listings.

"But wait", you say! "I need to previews views with my actual view model, or else how do I know my view model works properly and everything works together?"

If you need this spelling out, here's what you do:

1. **Unit test** your *View Model*, like a good developer.
2. **Preview** your *View*, the whole *(set of possible inputs to the) View* and nothing but the *View*.
3. **UI test** the integrated whole - this will be even easier when you can spin up new targets like nothing, so bear with me.

## Step 1 - Views should be Views

To build views in a truly testable way, you have to decouple the view from the view model. You could just add a protocol and call it a day, but you'll find yourself dealing with extra boilerplate code that scales with number of views if you do. SwiftUI views are best maintained, and most performant, with many small views composed together.

What does our view actually need? Let's do the unthinkable, and start by banning View Models altogether.

```swift

// View-agnostic model

// [...]

@Observable
class ContentManager { // Formerly ContentViewModel
    var thingA: Int
    var thingB: String
    private var apiResponse: WhoppingGreatAPIResponse
    private var apiHandler: APIHandler
    init(thingA: Int, thingB: String, apiResponse: WhoppingGreatAPIResponse, apiHandler: APIHandler) {
        self.thingA = thingA
        self.thingB = thingB
        self.apiResponse = apiResponse
        self.apiHandler = apiHandler
    }
    
    var outputText: String {
        if apiResponse.countFoos < 0 {
            "Hello world! Your number is \(thingA * 123) and your string is \(thingB)"
        } else {
            "Negative foos shouldn't happen"
        }
    }
    
    func submitRequest() async {
        await apiHandler.makeRequest()
    }
}

// View Model

// View

struct ContentView: View {
    var outputText: String
    var submitRequest: () async -> ()
    
    var body: some View {
        VStack {
            Text(outputText)
            Button("Do thing") {
                Task {
                    await submitRequest()
                }
            }
        }
    }
}

// Previews

#Preview("Success") {
    ContentView(outputText: "Hello world! Your number is 123 and your string is ABCD", submitRequest: {})
}

#Preview("Failure") {
    ContentView(outputText: "Negative foos shouldn't happen", submitRequest: {})
}
 
// Entry point for app, single page showcase app, in-app showcase listing, etc

struct DataView: View {
    @Bindable var contentManager: ContentManager
    
    var body: some View {
        ContentView(outputText: contentManager.outputText, submitRequest: contentManager.submitRequest)
    }
}

```

See, that wasn't so bad, was it? Our view model is still there, and look, our previews are so much cleaner than they were!

ContentView feels a bit like an extension of this new "DataView" at the moment, but this is actually a crucial step - we're moving our dependency injection closer to the entry point of our app. For more details on the benefits of this kind of approach, see ["Achieving Loose Coupling with Pure Dependency Injection and the Composition Root Pattern" by Simon B. Støvring, from SwiftLeeds 2023](https://www.youtube.com/watch?v=7zIKzOF6010).

However, there are some flaws with this approach - our views will start needing a whole lot of parameters as we start to scale the things we need to access. Let's solve that next.

## Step 2 - Making Models for our Views

To scale to many parameters, we're going to reintroduce View Models, but differently this time. Instead of being per-view, they'll simply group values we often need to use together. And instead of being a class, they'll be protocols, with only a Preview-prefixed mock defined alongside.

Our view simply doesn't care about backend logic at all. Our backend types will have to conform and compose according to the needs of the View, not the other way around. If it comes to it, to conform to these protocols we may need new backend types, new layers of model, which is fine if you follow proper unit-testable practices.

```swift

// View-agnostic model

// […]

extension ContentManager: TextGenerator, IODoohickey {}

// View Model

protocol TextGenerator: Observable {
    var outputText: String { get }
    // Etc, with related stuff a view might want
}

@Observable
class PreviewTextGenerator: TextGenerator {
    var outputText: String
    init(outputText: String = "Previews") {
        self.outputText = outputText
    }
    
    // Let's define common cases for use in previews!
    static let success = PreviewTextGenerator(outputText: "Hello world! Your number is 123 and your string is ABCD")
    static let failure = PreviewTextGenerator(outputText: "Negative foos shouldn't happen")
    static let crazyLong = PreviewTextGenerator(outputText: String(repeating: "Rhubarb ", count: 200))
}


protocol IODoohickey {
    func submitRequest() async
    // Etc, with related stuff a view might want
}

struct PreviewIODoohickey: IODoohickey {
    func submitRequest() async {
        // Do something interesting
    }
    
    static let noOp = Self()
}

// View

struct ContentView<TextGeneratorType: TextGenerator>: View {
    
    var textGenerator: TextGeneratorType // Generic param - may have sub-models, may be used in property wrappers
    var ioDoohickey: any IODoohickey // No sub-models or property wrappers possible
    
    var body: some View {
        VStack {
            Text(textGenerator.outputText)
            Button("Do thing") {
                Task {
                    await ioDoohickey.submitRequest()
                }
            }
        }
    }
}

// Previews

#Preview("Success") {
    ContentView(textGenerator: PreviewTextGenerator.success, ioDoohickey: PreviewIODoohickey.noOp)
}

#Preview("Failure") {
    ContentView(textGenerator: PreviewTextGenerator.failure, ioDoohickey: PreviewIODoohickey.noOp)
}

#Preview("Extreme data") {
    ContentView(textGenerator: PreviewTextGenerator.crazyLong, ioDoohickey: PreviewIODoohickey.noOp)
}

#Preview("No text") {
    ContentView(textGenerator: PreviewTextGenerator(outputText: ""), ioDoohickey: PreviewIODoohickey.noOp)
}

#Preview("Emoji") {
    ContentView(textGenerator: PreviewTextGenerator(outputText: "😝"), ioDoohickey: PreviewIODoohickey.noOp)
}

// Entry point for app, single page showcase app, in-app showcase listing, etc

struct DataView: View {
    @Bindable var contentManager: ContentManager
    
    var body: some View {
        ContentView(textGenerator: contentManager, ioDoohickey: contentManager)
    }
}

```

And just like that, we have the basics of reuse between previews, and we've even spun up some more because we can!

Notice that `ContentManager` conforms to both these protocols. This allows sharing of state between each our view's textGenerator and ioDoohickey, if for example our text is to update when we call `submitRequest()`. But because we're no longer tightly coupled, we could equally decompose `ContentManager` into two separate types if that sharing of state isn't needed, in fact it's encouraged with this pattern! Small views, and small models, it's a software architect's dream!

But we can go one step further…

## PreviewProvider is dead. Long live PreviewProvider!

By introducing a ContentView_Previews view (like we did in the days before macros), we begin to actively seek out more ways to use it! We just can't stop reusing our view over and over again!

```swift

// […]

// Previews

private struct ContentView_Previews: View {
    @Bindable var textGenerator = PreviewTextGenerator()
    var showControlPanel = false
    
    var body: some View {
        // Neat way to add a control panel to previews
        let view = ContentView(textGenerator: textGenerator, ioDoohickey: PreviewIODoohickey.noOp)
        if showControlPanel {
            ScrollView {
                view
            }.safeAreaInset(edge: .bottom, spacing: 0) { 
                if showControlPanel {
                    VStack {
                        Divider()
                        VStack {
                            TextField("Text", text: $textGenerator.outputText)
                        }
                        .padding()
                        .textFieldStyle(.roundedBorder)
                    }
                }
            }
        } else {
            view
        }
    }
}

#Preview("Success") {
    ContentView_Previews(textGenerator: .success)
}

#Preview("Failure") {
    ContentView_Previews(textGenerator: .failure)
}

#Preview("Extreme data") {
    ContentView_Previews(textGenerator: .crazyLong)
}

#Preview("No text") {
    ContentView_Previews(textGenerator: .init(outputText: ""))
}

#Preview("Emoji") {
    ContentView_Previews(textGenerator: .init(outputText: "😝"))
}

#Preview("Interactive") {
    ContentView_Previews(showControlPanel: true)
}

// Entry point for app, single page showcase app, in-app showcase listing, etc


struct ShowcaseEntry: View {
    var body: some View {
        ContentView_Previews(showControlPanel: true)
    }
}

struct ShowcaseMiniApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView_Previews(showControlPanel: true)
        }
    }
}

struct LiveMiniApp: App {
    @Bindable var contentManager: ContentManager
    
    var body: some Scene {
        WindowGroup {
            ContentView(textGenerator: contentManager, ioDoohickey: contentManager)
        }
    }
}

struct DataView: View {
    @Bindable var contentManager: ContentManager
    
    var body: some View {
        ContentView(textGenerator: contentManager, ioDoohickey: contentManager)
    }
}

```

---

Give it a try, and let me know what you think!