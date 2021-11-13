# swift-http-client

[![CI](https://github.com/uhooi/swift-http-client/actions/workflows/main.yml/badge.svg?branch=main)](https://github.com/uhooi/swift-http-client/actions/workflows/main.yml)
[![Release](https://img.shields.io/github/v/release/uhooi/swift-http-client)](https://github.com/uhooi/swift-http-client/releases/latest)
[![CocoaPods Version](https://img.shields.io/cocoapods/v/UHIHTTPClient.svg)](https://cocoapods.org/pods/UHIHTTPClient)
[![Carthage compatible](https://img.shields.io/badge/Carthage-compatible-4BC51D.svg)](https://github.com/uhooi/swift-http-client)
[![Swift Compatibility](https://img.shields.io/endpoint?url=https%3A%2F%2Fswiftpackageindex.com%2Fapi%2Fpackages%2Fuhooi%2Fswift-http-client%2Fbadge%3Ftype%3Dswift-versions)](https://swiftpackageindex.com/uhooi/swift-http-client)
[![Platform Compatibility](https://img.shields.io/endpoint?url=https%3A%2F%2Fswiftpackageindex.com%2Fapi%2Fpackages%2Fuhooi%2Fswift-http-client%2Fbadge%3Ftype%3Dplatforms)](https://swiftpackageindex.com/uhooi/swift-http-client)
[![License](https://img.shields.io/github/license/uhooi/swift-http-client)](https://github.com/uhooi/swift-http-client/blob/main/LICENSE)
[![Twitter](https://img.shields.io/twitter/follow/the_uhooi?style=social)](https://twitter.com/the_uhooi)

Communicate via HTTP easily in Swift.

## Table of Contents

- [Installation](#installation)
- [How to use](#how-to-use)
- [Contribution](#contribution)

## Installation

### Swift Package Manager (Recommended)

#### Package

You can add this package to `Package.swift`, include it in your target dependencies.

```swift
let package = Package(
    dependencies: [
        .package(url: "https://github.com/uhooi/swift-http-client", .upToNextMajor(from: "0.5.0")),
    ],
    targets: [
        .target(
            name: "<your-target-name>",
            dependencies: ["HTTPClient"]),
    ]
)
```

#### Xcode

You can add this package on Xcode.
See [documentation](https://developer.apple.com/documentation/swift_packages/adding_package_dependencies_to_your_app).

### CocoaPods

This library is available through [CocoaPods](https://cocoapods.org). To install it, simply add the following line to your `Podfile`:

```ruby
pod 'UHIHTTPClient', '~> 0.5.0'
```

### Carthage

This library is available through [Carthage](https://github.com/Carthage/Carthage). To install it, simply add the following line to your `Cartfile`:

```
github "uhooi/swift-http-client" ~> 0.5.0
```

## How to use

You can just import `HTTPClient` to use it.

1. Implement a request body structure that conforms to the `Encodable` protocol. (Optional)

    ```swift
    struct RegisterUserRequestBody: Encodable {
        let name: String
        let description: String
    }
    ```

2. Implement a response body structure that conforms to the `Decodable` protocol.

    ```swift
    struct RegisterUserResponseBody: Decodable {
        let id: String
    }
    
    extension RegisterUserResponseBody {
        func convertToUserID() -> UserID { .init(id: self.id) }
    }
    ```

3. Implement a request structure that conforms to the `Request` protocol.

    ```swift
    import HTTPClient
    
    struct RegisterUserRequest: Request {
        typealias ResponseBody = RegisterUserResponseBody
        var path: String { "user/create_user" }
        var httpMethod: HTTPMethod { .post }
        var httpHeaders: [HTTPHeaderField: String]? { [.contentType: ContentType.applicationJson.rawValue] }
    }
    ```

4. Create an instance of the `HTTPClient` class and call the `request` method.

    ```swift
    struct UserID: Identifiable, Equatable {
        let id: String
    }
    ```

    ```swift
    protocol VersatileRepository {
        func registerUser(name: String, description: String, completion: @escaping (Result<UserID, Error>) -> Void)
    }
    ```

    ```swift
    import HTTPClient
    
    final class VersatileAPIClient {
        static let shared = VersatileAPIClient()
        
        private let httpClient = HTTPClient(baseURLString: "https://example.com/api/")
    }
    
    extension VersatileAPIClient: VersatileRepository {
        func registerUser(name: String, description: String, completion: @escaping (Result<UserID, Error>) -> Void) {
            let requestBody = RegisterUserRequestBody(name: name, description: description)
            httpClient.request(RegisterUserRequest(), requestBody: requestBody) { result in
                switch result {
                case let .success(responseBody):
                    completion(.success(responseBody.convertToUserID()))
                case let .failure(error):
                    completion(.failure(error))
                }
            }
        }
    }
    ```

    ```swift
    VersatileAPIClient.shared.registerUser(name: "Uhooi", description: "Green monster.") { result in
        switch result {
        case let .success(userID):
            // Do something.
        case let .failure(error):
            // Do error handling.
        }
    }
    ```

## Contribution

I would be happy if you contribute :)

- [New issue](https://github.com/uhooi/swift-http-client/issues/new)
- [New pull request](https://github.com/uhooi/swift-http-client/compare)
