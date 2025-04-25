## Swift100DaysProjects
Following the 100 days of Swift tutorials from twostraws: https://www.hackingwithswift.com/100

# Project 1 WeSplit: Split the bill to different people according to the tip percentage

# Video:[▶️ Watch Demo Video on YouTube](https://youtube.com/shorts/K1w71587W8c?feature=share)

# Code:
```swift
//
//  ContentView.swift
//  WeSplit
//
//  Created by Suqing Liu on 2025-04-25.
//

import SwiftUI

struct ContentView: View {
    @State private var checkAmount = 0.0
    @State private var numberOfPeople = 2
    @State private var tipPercentage = 20
    let tipPercentages = [0, 10, 13, 15, 18, 20]
    
    @FocusState private var amountIsFocused: Bool

    
    var totalPerPerson: Double {
        let peopleCount = Double(numberOfPeople)
        let tipSelection = Double(tipPercentage)

        let tipValue = checkAmount / 100 * tipSelection
        let grandTotal = checkAmount + tipValue
        let amountPerPerson = grandTotal / peopleCount
        
        return amountPerPerson
    }

    var body: some View {
        NavigationStack {
            Form {
                Section {
                    TextField("Amount", value: $checkAmount, format: .currency(code: Locale.current.currency?.identifier ?? "CAD"))
                        .keyboardType(.decimalPad)
                        .focused($amountIsFocused)

                }
                    
                Section("How much tip do you want to leave?") {
                    Picker("Tip percentage", selection: $tipPercentage) {
                        ForEach(tipPercentages, id: \.self) {
                            Text($0, format: .percent)
                        }
                    }
                    .pickerStyle(.segmented)
                }
                
                Section{
                    
                    Picker("Number of People", selection: $numberOfPeople) {
                        ForEach(2...12, id: \.self) {
                            Text("\($0) People")
                        }
                    }
                }
                
                Section {
                    Text(totalPerPerson, format: .currency(code: Locale.current.currency?.identifier ?? "USD"))
                }
            }
            .navigationTitle("WeSplit")
            .toolbar {
                if amountIsFocused {
                    Button("Done") {
                        amountIsFocused = false
                    }
                }
            }

        }


    }
}

#Preview {
    ContentView()
}
```

# Project 2 LengthConverter: Apply Length Conversion in between of "m", "km", "feet", "inches", "yards", and "miles".

# Video:[▶️ Watch Demo Video on YouTube](https://youtube.com/shorts/bTncWYRVUGA?feature=share)

# Code:
```Swift
//
//  ContentView.swift
//  LengthConverter
//
//  Created by Suqing Liu on 2025-04-25.
//

import SwiftUI

struct ContentView: View {
    private let units = ["m", "km", "feet", "inches", "yards", "miles"]
    @State private var inUnit: String = "m"
    @State private var outUnit: String = "km"
    @State private var inLength: Double = 0
    
    private func toMetres(_ value: Double, unit: String) -> Double {
        switch unit {
        case "m":      return value
        case "km":     return value * 1_000
        case "feet":   return value * 0.3048
        case "inches": return value * 0.0254
        case "yards":  return value * 0.9144
        case "miles":  return value * 1_609.344
        default:       return value
        }
    }
    
    private func fromMetres(_ value: Double, unit: String) -> Double {
        switch unit {
        case "m":      return value
        case "km":     return value / 1_000
        case "feet":   return value / 0.3048
        case "inches": return value / 0.0254
        case "yards":  return value / 0.9144
        case "miles":  return value / 1_609.344
        default:       return value
        }
    }
    
    private var converted: Double {
        let metres = toMetres(inLength, unit: inUnit)
        return fromMetres(metres, unit: outUnit)
    }

    var body: some View {
        NavigationStack {
            Form {
                Section("Input") {
                    TextField("Length",
                              value: $inLength,
                              format: .number)
                        .keyboardType(.decimalPad)
                    
                    Picker("From", selection: $inUnit) {
                        ForEach(units, id: \.self) { Text($0.capitalized) }
                    }
                    .pickerStyle(.segmented)
                }
                
                Section("Output") {
                    Text(converted.formatted())
                    
                    Picker("To", selection: $outUnit) {
                        ForEach(units, id: \.self) { Text($0.capitalized) }
                    }
                    .pickerStyle(.segmented)
                }
            }
            .navigationTitle("Length Converter")
        }
    }
}

#Preview {
    ContentView()
}

```



