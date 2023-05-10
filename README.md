# navigation_stack_global_routing

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
