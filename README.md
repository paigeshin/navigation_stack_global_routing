# navigation_stack_global_routing

### Concept 

1. You need to pass `hashable` to link [NavigationLink(value: ...)] and attach navigation destination modifier to receive the value and route  
2. Or you can create global `Path` Object which is also hashable and assign it to NavigationStack and attach navigation destination modifier to receive the path and route 
3. Or you can use `NavigationPath` and assign it to NavigationStack. It behaves like Generic which can take any data type.

### Using Path

```swift
import SwiftUI

struct ContentView: View {
    
    // MARK: CREATING NAVIGATION STACK
    // WHERE YOU CAN PUSH AND POP VIEWS
    
    @State var mainStack: [NavigationType] = []
    
    var body: some View {
        NavigationStack(path: self.$mainStack) {
            
            TabView {
                
                Text("Home")
                    .tabItem {
                        Image(systemName: "house.fill")
                    }
                
                Text("Search")
                    .tabItem {
                        Image(systemName: "magnifyingglass")
                    }
                
                Text("Liked")
                    .tabItem {
                        Image(systemName: "suit.heart.fill")
                    }
                
                Text("Settings")
                    .tabItem {
                        Image(systemName: "person.circle.fill")
                    }
                
            } //: TabView
            .navigationTitle("Instagram")
            .navigationBarTitleDisplayMode(.inline)
            .toolbar {
                ToolbarItem(placement: .navigationBarTrailing) {
                    Button {
                        self.mainStack.append(.dm)
                    } label: {
                        Image(systemName: "paperplane")
                            .font(.callout)
                    }
                }
                ToolbarItem(placement: .navigationBarLeading) {
                    Button {
                        self.mainStack.append(.camera)
                    } label: {
                        Image(systemName: "camera")
                            .font(.callout)
                    }
                }
            }
            // MARK: NEW API THAT CAN PUSH NEW BASED ON DATATYPE
            .navigationDestination(for: NavigationType.self) { value in
                switch value {
                case .camera:
                    VStack {
                        Text("Camera View")
                        Button("Pop") {
                            self.mainStack.removeLast()
                        }
                        Button("Go to DM View") {
                            self.mainStack.append(.dm)
                        }
                    }
                case .home: Text("Home View")
                case .dm: Text("DM View")
                    VStack {
                        Text("DM View")
                        Button("Pop") {
                            self.mainStack.removeLast()
                        }
                        Button("Go to Root") {
                            self.mainStack.removeAll()
                        }
                    }
                }
            }
            
        }
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}

// MARK: Enum Case for Navigation Stack Items
enum NavigationType: String, Hashable {
    case dm = "DM"
    case camera = "CAMERA"
    case home = "HOME"
}
```

### Using NavigationLink()

```swift
//
//  GlobalRoutingApp.swift
//  GlobalRouting
//
//  Created by paige shin on 2023/05/11.
//

import SwiftUI

struct Movie: Hashable {
    let name: String
}

struct Review: Hashable {
    let text: String
}

enum Route: Hashable {
    case list
    case detail(Movie)
    case reviews([Review])
}

@main
struct GlobalRoutingApp: App {
    var body: some Scene {
        WindowGroup {
            NavigationStack {
                MovieListScreen()
                    .navigationDestination(for: Route.self) { route in
                        switch route {
                        case .list:
                            MovieListScreen()
                        case .detail(let movie):
                            MovieDetailScreen(movie: movie)
                        case .reviews(let reviews):
                            ReviewListScreen(reviews: reviews)
                        }
                    }
            }
        }
    }
}


struct MovieListScreen: View {
    
    let movies: [Movie] = [
        .init(name: "Spiderman"),
        .init(name: "Batman"),
    ]
    
    var body: some View {
        List(self.movies, id: \.name) { movie in
            NavigationLink(movie.name, value: Route.detail(movie))
        }
    }
}

struct MovieDetailScreen: View {
    
    let movie: Movie
    
    var body: some View {
        VStack {
            Text(self.movie.name)
                .font(.largeTitle)
            NavigationLink("Reviews", value: Route.reviews([Review(text: "Good Movie!")]))
        }
    }
    
}

struct ReviewListScreen: View {
    
    let reviews: [Review]
    
    var body: some View {
        List(self.reviews, id: \.text) { review in
            Text(review.text)
        }
    }
    
}

```
