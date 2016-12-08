#CardParser 

*Credit Card Type Parsing for Swift*

CardParser is a lightweight utility for identifying major credit card types from in-progress input. This is useful for building responsive credit entry forms and validating input.

## Usage

For now, just download and add `CardParser.swift` to your project.

### The Basics

To determine the state of some in-progress text:

```swift
switch CardState(fromPrefix: cardString) {

case .identified(let card):
  //do something with card

case .indeterminate(let possibleCards):
  //do something with possibleCards

case .invalid:
  //show some validation error

}
```

Or if you are just concerned with the validity of some completed input:
```swift
guard CardState(fromNumber: cardNumber) != .invalid else {
    //show some validation error
}
```

### Under The Hood

`CardParser.swift` contains the enums:
- `CardType` 
- `CardState`

### Card Type
`CardType` encapsulates credit card issuers and their validation/ formatting requirements
```swift

enum CardType {
    case amex
    case diners
    case discover
    case jcb
    case masterCard
    case visa
}
```
You can interact with an extracted card type to update your form:
```swift
let visa: CardType = .visa
visa.isPrefixValid("4") //true
visa.isValid("4")       //false
visa.cvvLength          //3
visa.segmentGroupings   //[4, 4, 4, 4]
visa.maxLength          //19
```

### Card State
`CardState` can be leveraged to identify the `CardType` (or possible `CardType`s) of some input.
```swift
enum CardState {
    case identified(CardType)
    case indeterminate([CardType])
    case invalid
}
```
Use `CardState` to extract state and type information
```swift
CardState(fromPrefix: "4")  //.identified(.visa)
CardState(fromPrefix: "3")  //.indeterminate([.amex, .diners, .jcb])
CardState(fromPrefix: "0")  //.invalid
```
For example, you may write a method to return a card image for your form:
```swift
func cardImage(forState cardState:CardState) -> UIImage? {
    switch cardState {
    case .identified(let cardType):
        switch cardType{
        case .visa:         return #imageLiteral(resourceName: "credit_cards_visa")
        case .masterCard:   return #imageLiteral(resourceName: "credit_cards_mastercard")
        case .amex:         return #imageLiteral(resourceName: "credit_cards_americanexpress")
        case .diners:       return #imageLiteral(resourceName: "credit_cards_diners")
        case .discover:     return #imageLiteral(resourceName: "credit_cards_discover")
        case .jcb:          return #imageLiteral(resourceName: "credit_cards_jcb")
        }
    case .indeterminate: return #imageLiteral(resourceName: "credit_cards_generic")
    case .invalid:      return #imageLiteral(resourceName: "credit_cards_invalid")
    }
}
```

## Author

Jay Clark: <mailto:jason.clark@raizlabs.com>

## License

CardParser is available under the MIT license. See the LICENSE file for more info.
