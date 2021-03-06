# TableTie   
![Build Status](https://img.shields.io/travis/vladimirkofman/TableTie/master.svg?style=flat-square)
[![CocoaPods Compatible](https://img.shields.io/cocoapods/v/TableTie.svg)](https://img.shields.io/cocoapods/v/TableTie.svg)
[![Carthage Compatible](https://img.shields.io/badge/Carthage-compatible-4BC51D.svg?style=flat)](https://github.com/Carthage/Carthage)
[![Code coverage status](https://img.shields.io/codecov/c/github/vladimirkofman/TableTie.svg?style=flat-square)](http://codecov.io/github/vladimirkofman/TableTie)
[![CocoaPods](https://img.shields.io/cocoapods/metrics/doc-percent/TableTie.svg?maxAge=3600)](http://cocoadocs.org/docsets/TableTie)
![Platform support](https://img.shields.io/badge/platform-ios-lightgrey.svg?style=flat-square)
[![License MIT](https://img.shields.io/badge/license-MIT-blue.svg?style=flat-square)](https://github.com/ReSwift/ReSwift/blob/master/LICENSE.md)
### A quick, generic way to populate a `UITableView` with data.  
*Inspired by
[Generic Table View Controllers](https://talk.objc.io/episodes/S01E26-generic-table-view-controllers-part-2 "Swift Talk") by [objc.io](https://www.objc.io/)*

## Features
* Reduces boilerplate code by implementing the most [frequently used set](#more-options) of `UITableViewDelegate` and `UITableViewDataSource` methods.
* No need to change your models.
* Supports multiple cell types in the same `UITableView`.
* Supports cells created in code or in a storyboard.
* Automatic cell registration for reuse.

## Usage
Let's say you have a list of Songs and Albums that you want to display in `UITableView`. Your models can be structs, classes, enums, or just Strings. Let's use structs for the example:
```swift
struct Song {
    let title: String
}

struct Album {
    let name: String
    let artist: String
}

class SongCell: UITableViewCell {}
class AlbumCell: UITableViewCell {}
```
You'll need your models to conform to TableTie.Row protocol. There's only one required method that you'll need to implement: `configure(cell:)`. In this method you'll configure your cell according to your model. Make sure to use the correct type for the `cell:` parameter: it's generic, and will work for any subclass of `UITableViewCell`.

```swift
extension Song: TableTie.Row {
    func configure(cell: SongCell) {
        cell.textLabel?.text = title
    }
}

extension Album: TableTie.Row {
    func configure(cell: AlbumCell) {
        cell.textLabel?.text = "\(name) by \(artist)"
    }
}
```
In your view controller, you'll need to create an instance of `TableTie.Adapter`, and provide it with the array of your models.
```swift
class MyVC: UITableViewController {  
    let tieAdapter =
      TableTie.Adapter([
          Album(name: "Paranoid", artist:"Black Sabbath"),
          Song(title: "War Pigs"),
          Song(title: "Paranoid"),
          Song(title: "Planet Caravan")])

    override func viewDidLoad() {
        tableView.delegate = tieAdapter
        tableView.dataSource = tieAdapter

        super.viewDidLoad()
    }
}
```
Don't forget to set .delegate and .dataSource for the tableView to the `TableTie.Adapter` instance you've created earlier.    

#### And that's all!  
*Please read on for more options...*

### Sections
```swift
...
let tieAdapter = TableTie.Adapter([
        TableTie.Section("Album", [
            Album(name: "Paranoid", artist:"Black Sabbath"),
        ]),
        TableTie.Section("Side one", [
            Song(title: "War Pigs"),
            Song(title: "Paranoid"),
            Song(title: "Planet Caravan"),
            Song(title: "Iron Man"),
        ])])
...
```

### Custom reuseIdentifier and Storyboard support
`TableTie` will automagically register your cell for `UITableViewCell` reuse. If you need to use a specific `reuseIdentifier`, or if you've designed your cell in a storyboard, just override the `reuseIdentifier` property of `TableTie.Row`:
```swift
extension Song: TableTie.Row {
    var reuseIdentifier: String { return "reuseMePlease" } // <-- HERE

    func configure(cell: YourStoryboardCell) {
        //Do what you have to do here...
    }
}
```

### Row height
```swift
extension Song: TableTie.Row {
    var reuseIdentifier: String { return "reuseMePlease" }
    var rowHeight: CGFloat { return 100.0 } // <-- HERE

    func configure(cell: YourStoryboardCell) {
        //Do what you have to do here...
    }
}
```

### Row selection
* **Option 1:** Use `TableTie.SelectableRow` wrapper
```swift
...
    tieAdapter.set([
        SelectableRow(Song(title:"Paranoid"), self.playSong("Paranoid")),
        SelectableRow(Song(title:"Iron Man"), self.playSong("Iron Man")),
    ])
...
```
* **Option 2:** Override `didSelectRow` for the row
```swift
extension Song: TableTie.Row {
...
    func didSelectRow(of tableView: UITableView, at indexPath: IndexPath) {
        // Implement your logic here...
    }
...
}
```
* **Option 3:** Set `didSelect` closure for `TableTie.Adapter`
```swift
...
tieAdapter.didSelect = { row, tableView, indexPath in
    // Implement your logic here...
}
...
```

## More options
`TableTie` provides a quick and simple solution for displaying arbitrary data in `UITableView` by implementing most frequently used set of  `UITableViewDataSource` and `UITableViewDelegate` methods. For more advanced options, you can subclass `TableTie.Adapter` and implement (or override) the relevant methods in your subclass.

`TableTie.Adapter` implements the following methods:

#### UITableViewDataSource
* `numberOfSections(in:)`
* `tableView(_:numberOfRowsInSection:)`
* `tableView(_:cellForRowAt:)`
* `tableView(_:titleForHeaderInSection:)`
* `tableView(_:titleForFooterInSection:)`

#### UITableViewDelegate
* `tableView(_:heightForRowAt:)`
* `tableView(_:didSelectRowAt:)`

***

## Installation

### CocoaPods

You can install TableTie via CocoaPods by adding it to your `Podfile`:
```
pod 'TableTie'
```

And run `pod install`.

### Carthage

Add this to your Cartfile:
```
github "vladimirkofman/TableTie"
```

Then run `carthage update`.

## License

TableTie is available under the MIT license. See the LICENSE file for more info.
