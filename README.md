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

# Project 3 GuessFlgas: Guess the Flag of a country

# Video:[▶️ Watch Demo Video on YouTube](https://youtube.com/shorts/vNgz5aPvqzY?feature=share)

# Code:
```Swift
//
//  ContentView.swift
//  GuessFlags
//
//  Created by Suqing Liu on 2025-04-25.
//

import SwiftUI

struct ContentView: View {
    @State private var countries = ["Estonia", "France", "Germany", "Ireland", "Italy", "Nigeria", "Poland", "Spain", "UK", "Ukraine", "US"].shuffled()
    @State private var correctAnswer = Int.random(in: 0...2)
    @State private var showingScore = false
    @State private var scoreTitle = ""
    @State private var score = 0
    @State private var rounds = 1
    @State private var showingEnd = false

    var body: some View {
        ZStack {
            RadialGradient(stops: [
                .init(color: Color(red: 0.1, green: 0.2, blue: 0.45), location: 0.3),
                .init(color: Color(red: 0.76, green: 0.15, blue: 0.26), location: 0.3),
            ], center: .top, startRadius: 200, endRadius: 400)
                .ignoresSafeArea()
            VStack {
                Spacer()
                Text("Guess the Flag: Round \(rounds)")
                    .font(.largeTitle.weight(.bold))
                    .foregroundStyle(.white)
                
                VStack(spacing: 15) {
                    VStack {
                        Text("Tap the flag of")
                            .foregroundStyle(.secondary)
                            .font(.subheadline.weight(.heavy))

                        Text(countries[correctAnswer])
                            .font(.largeTitle.weight(.semibold))
                    }
                    
                    ForEach(0..<3) { number in
                        Button {
                            flagTapped(number)
                        } label: {
                            Image(countries[number])
                                .clipShape(.capsule)
                                .shadow(radius: 5)


                        }
                    }
                }
                .frame(maxWidth: .infinity)
                .padding(.vertical, 20)
                .background(.regularMaterial)
                .clipShape(.rect(cornerRadius: 20))
            }
            .padding()

        }
        .alert(scoreTitle, isPresented: $showingScore) {
            Button("Continue", action: askQuestion)
        } message: {
            Spacer()
            Spacer()
            Text("Score: \(score)")
                .foregroundStyle(.white)
                .font(.title.bold())
            Spacer()
        }
        .alert("Game Completed", isPresented: $showingEnd) {
            Button("Restart", action: restart)
        } message: {
            Text("\(score) is your final score!!!")
        }

    }
    
    func flagTapped(_ number: Int) {
        if number == correctAnswer {
            scoreTitle = "Correct!!!"
            score += 1
        } else {
            scoreTitle = "Wrong, that's the flag of \(countries[number])"
            score -= 1
        }
        showingScore = true
    }
    
    func askQuestion() {
        countries.shuffle()
        correctAnswer = Int.random(in: 0...2)
        if rounds == 8 {
            showingEnd = true
        } else {
            rounds += 1
        }
    }
    
    func restart() {
        askQuestion()
        rounds = 1
        score = 0
    }
}

#Preview {
    ContentView()
}

```



