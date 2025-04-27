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

# Video:[▶️ Watch Demo Video on YouTube](https://youtube.com/shorts/oefurXsVJVQ?feature=share)

# Code: (Requires Model Training with apple's Create ML, csv file is provided in the handout)
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
# Project 4 BetterRest, a coffee drinking tracker to recommend you when to sleep based on Machine Learning

# Video:[▶️ Watch Demo Video on YouTube](https://youtube.com/shorts/vNgz5aPvqzY?feature=share)

# Code:
```Swift
//
//  ContentView.swift
//  BetterRest
//
//  Created by Suqing Liu on 2025-04-25.
//

import CoreML
import SwiftUI

struct ContentView: View {
    // MARK: – State
    @State private var wakeUp        = Date.now
    @State private var sleepAmount   = 8.0
    @State private var coffeeAmount  = 1          // 1‥20 cups

    // MARK: – Computed bedtime
    private var idealBedtime: Date? {
        do {
            let model = try SleepCalculator(configuration: .init())
            let comps = Calendar.current.dateComponents([.hour, .minute], from: wakeUp)
            let seconds = (comps.hour ?? 0) * 3_600 + (comps.minute ?? 0) * 60
            let pred = try model.prediction(
                wake: Double(seconds),
                estimatedSleep: sleepAmount,
                coffee: Double(coffeeAmount)
            )
            return wakeUp - pred.actualSleep
        } catch {
            return nil
        }
    }

    // MARK: – UI
    var body: some View {
        NavigationStack {
            Form {
                // Wake-up time
                Section("When do you want to wake up?") {
                    HStack {
                        Spacer()
                        DatePicker(
                            "",
                            selection: $wakeUp,
                            displayedComponents: .hourAndMinute
                        )
                        .labelsHidden()
                        .datePickerStyle(.wheel)          // big wheel selector
                        .font(.title2)                    // larger digits
                        .frame(maxWidth: .infinity)       // centered
                        Spacer()
                    }
                }

                // Desired sleep
                Section("Desired amount of sleep") {
                    Stepper(
                        "\(sleepAmount.formatted()) hours",
                        value: $sleepAmount,
                        in: 4...12,
                        step: 0.25
                    )
                }

                // Coffee intake (now a Stepper)
                Section("Daily coffee intake") {
                    Stepper(
                        "\(coffeeAmount) cup\(coffeeAmount == 1 ? "" : "s")",
                        value: $coffeeAmount,
                        in: 1...20
                    )
                }

                // Recommended bedtime
                Section("Recommended bedtime") {
                    Text(
                        idealBedtime?.formatted(date: .omitted, time: .shortened)
                        ?? "—"
                    )
                    .font(.system(size: 40, weight: .bold))
                    .frame(maxWidth: .infinity, alignment: .center)
                    .padding(.vertical, 8)
                }
            }
            .navigationTitle("BetterRest")
        }
    }
}

#Preview {
    ContentView()
}

```
# Project 5 WordScramble, a word combining game, try to build as many words with 8-character words.

# Video:[▶️ Watch Demo Video on YouTube](https://youtube.com/shorts/rdzDFe0pmgc?feature=share)

# Code:
```Swift
//
//  ContentView.swift
//  WordScramble
//
//  Created by Suqing Liu on 2025-04-26.
//

import SwiftUI

struct ContentView: View {
    @State private var usedWords = [String]()
    @State private var rootWord = ""
    @State private var newWord = ""
    
    @State private var errorTitle = ""
    @State private var errorMessage = ""
    @State private var showingError = false
    
    var body: some View {
        NavigationStack {
            List {
                Section {
                    TextField("Enter your word", text: $newWord)
                        .textInputAutocapitalization(.never)

                }

                Section {
                    ForEach(usedWords, id: \.self) { word in
                        HStack {
                            Image(systemName: "\(word.count).circle")
                            Text(word)
                        }
                    }
                }
            }
            .navigationTitle(rootWord)
            .onSubmit(addNewWord)
            .onAppear(perform: startGame)
            .alert(errorTitle, isPresented: $showingError) {
                Button("OK") { }
            } message: {
                Text(errorMessage)
            }
            .toolbar {
                Button("Restart") {
                    startGame()
                }
            }
        }
    }
    
    func addNewWord() {
        // lowercase and trim the word, to make sure we don't add duplicate words with case differences
        let answer = newWord.lowercased().trimmingCharacters(in: .whitespacesAndNewlines)

        // exit if the remaining string is empty
        guard answer.count > 0 else { return }
        
        guard answer.count >= 3 else {
            wordError(title: "Word too short", message: "Words must be at least 3 letters long.")
            return
        }
        
        guard answer != rootWord else {
            wordError(title: "Word same as root word", message: "You can't just use the original word!")
            return
        }

        guard isOriginal(word: answer) else {
            wordError(title: "Word used already", message: "Be more original")
            return
        }

        guard isPossible(word: answer) else {
            wordError(title: "Word not possible", message: "You can't spell that word from '\(rootWord)'!")
            return
        }

        guard isReal(word: answer) else {
            wordError(title: "Word not recognized", message: "You can't just make them up, you know!")
            return
        }

        withAnimation {
            usedWords.insert(answer, at: 0)
        }
        newWord = ""
    }
    
    func startGame() {
        usedWords = [String]()
        newWord = ""
        
        if let startWordsURL = Bundle.main.url(forResource: "start", withExtension: "txt") {
            if let data = try? Data(contentsOf: startWordsURL) {
                let startWords = String(decoding: data, as: UTF8.self)
                
                let allWords = startWords.components(separatedBy: .newlines)
                
                rootWord = allWords.randomElement() ?? "silkworm"
                return
            }
        }
        fatalError("Could not load start.txt from bundle.")
    }
    
    func isOriginal(word: String) -> Bool {
        !usedWords.contains(word)
    }
    
    func isPossible(word: String) -> Bool {
        // Build frequency table for rootWord  ➜  O(|rootWord|)
        var available = [Character: Int]()
        for ch in rootWord {
            available[ch, default: 0] += 1
        }
        
        // Check each letter in word           ➜  O(|word|)
        for ch in word {
            guard let remaining = available[ch], remaining > 0 else {
                return false          // letter missing or exhausted
            }
            available[ch] = remaining - 1
        }
        return true
    }
    
    func isReal(word: String) -> Bool {
        let checker = UITextChecker()
        let range = NSRange(location: 0, length: word.utf16.count)
        let misspelledRange = checker.rangeOfMisspelledWord(in: word, range: range, startingAt: 0, wrap: false, language: "en")

        return misspelledRange.location == NSNotFound
    }
    
    func wordError(title: String, message: String) {
        errorTitle = title
        errorMessage = message
        showingError = true
    }

}

#Preview {
    ContentView()
}
```
# Project 6 Animation Multiplication Table Quizer, from 2 to 9

# Video:[▶️ Watch Demo Video on YouTube](https://youtube.com/shorts/aNeIsPhZa0Q)

# Code:
```Swift
import SwiftUI

// MARK: - Data Model
struct Question: Identifiable {
    let id = UUID()
    let text: String
    let answer: Int
}

// MARK: - Root View
struct ContentView: View {
    
    enum AppState: Equatable {
        case settings
        case playing
        case finished(score: Int, total: Int)
    }

    // Game-level state
    @State private var appState: AppState = .settings
    @State private var maxTable = 12
    @State private var questionCount = 10

    // Round-to-round state
    @State private var questions: [Question] = []
    @State private var currentIndex = 0
    @State private var userAnswer = ""
    @State private var score = 0

    var body: some View {
        Group {
            switch appState {
            case .settings:
                SettingsView(
                    maxTable: $maxTable,
                    questionCount: $questionCount,
                    startAction: startGame
                )

            case .playing:
                GameView(
                    question: questions[currentIndex],
                    index: currentIndex + 1,
                    total: questions.count,
                    userAnswer: $userAnswer,
                    submitAction: submitAnswer
                )

            case .finished(let finalScore, let total):
                ResultView(score: finalScore, total: total) {
                    appState = .settings
                }
            }
        }
        .animation(.default, value: appState)
    }

    // MARK: - Game Flow Helpers
    private func startGame() {
        score = 0
        userAnswer = ""
        currentIndex = 0
        questions = makeQuestions()
        appState = .playing
    }

    private func submitAnswer() {
        guard let answerInt = Int(userAnswer) else { return }
        if answerInt == questions[currentIndex].answer {
            score += 1
        }
        userAnswer = ""
        currentIndex += 1
        if currentIndex == questions.count {
            appState = .finished(score: score, total: questions.count)
        }
    }

    private func makeQuestions() -> [Question] {
        var output: [Question] = []
        let range = 2...maxTable
        while output.count < questionCount {
            let left  = Int.random(in: range)
            let right = Int.random(in: range)
            output.append(
                Question(
                    text: "\(left) × \(right) = ?",
                    answer: left * right
                )
            )
        }
        return output
    }
}

// MARK: - Settings Screen
struct SettingsView: View {
    @Binding var maxTable: Int
    @Binding var questionCount: Int
    var startAction: () -> Void

    private let questionOptions = [5, 10, 20]

    var body: some View {
        NavigationView {
            Form {
                Section(header: Text("Tables up to")) {
                    Stepper(value: $maxTable, in: 2...12) {
                        Text("\(maxTable)")
                    }
                }
                Section(header: Text("Number of questions")) {
                    Picker("Question count", selection: $questionCount) {
                        ForEach(questionOptions, id: \.self) {
                            Text("\($0)")
                        }
                    }
                    .pickerStyle(SegmentedPickerStyle())
                }
                Section {
                    Button("Start Practice", action: startAction)
                        .frame(maxWidth: .infinity, alignment: .center)
                }
            }
            .navigationTitle("Multiplication Practice")
        }
    }
}

// MARK: - Game Screen
struct GameView: View {
    let question: Question
    let index: Int
    let total: Int
    @Binding var userAnswer: String
    var submitAction: () -> Void

    var body: some View {
        VStack(spacing: 30) {
            Text("Question \(index) / \(total)")
                .font(.headline)

            Text(question.text)
                .font(.largeTitle.weight(.bold))

            TextField("Your answer", text: $userAnswer)
                .keyboardType(.numberPad)
                .textFieldStyle(RoundedBorderTextFieldStyle())
                .frame(width: 150)
                .multilineTextAlignment(.center)

            Button("Submit", action: submitAction)
                .buttonStyle(.borderedProminent)
        }
        .padding()
    }
}

// MARK: - Result Screen
struct ResultView: View {
    let score: Int
    let total: Int
    var playAgain: () -> Void

    var body: some View {
        VStack(spacing: 20) {
            Text("Great job!")
                .font(.largeTitle.bold())

            Text("You scored \(score) out of \(total)")
                .font(.title2)

            Button("Play Again", action: playAgain)
                .buttonStyle(.borderedProminent)
        }
        .padding()
    }
}



#Preview {
    ContentView()
}
```
# Project 7 iExpense: Expence Tracker for Business and Personal Items.

# Video:[▶️ Watch Demo Video on YouTube](https://youtube.com/shorts/Wa1JffUAw_8?feature=share)

# Code:
```Swift
//
//  ContentView.swift
//  iExpense
//

import SwiftUI
import Observation               // ⬅️ for @Observable


// MARK: - Model
struct ExpenseItem: Identifiable, Codable {
    var id = UUID()
    let name: String
    let type: String          // "Business" or "Personal"
    let amount: Double
}

@Observable
class Expenses {
    var items = [ExpenseItem]() {
        didSet { save() }
    }

    init() { load() }

    // MARK: - Persistence
    private func load() {
        if let data = UserDefaults.standard.data(forKey: "Items"),
           let decoded = try? JSONDecoder().decode([ExpenseItem].self, from: data) {
            items = decoded
        }
    }

    private func save() {
        if let encoded = try? JSONEncoder().encode(items) {
            UserDefaults.standard.set(encoded, forKey: "Items")
        }
    }
}

// MARK: - Root View
struct ContentView: View {
    // state
    @State private var expenses = Expenses()
    @State private var showingAddExpense = false

    // convenience
    private let currencyCode = Locale.current.currency?.identifier ?? "USD"

    // pre-filtered arrays for the two sections
    private var personalItems: [ExpenseItem] {
        expenses.items.filter { $0.type == "Personal" }
    }
    private var businessItems: [ExpenseItem] {
        expenses.items.filter { $0.type == "Business" }
    }

    var body: some View {
        NavigationStack {
            List {
                // PERSONAL
                Section("Personal") {
                    ForEach(personalItems) { itemRow($0) }
                        .onDelete { delete(personalItems, at: $0) }
                }

                // BUSINESS
                Section("Business") {
                    ForEach(businessItems) { itemRow($0) }
                        .onDelete { delete(businessItems, at: $0) }
                }
            }
            .navigationTitle("iExpense")
            .toolbar {
                Button {
                    showingAddExpense = true
                } label: {
                    Label("Add Expense", systemImage: "plus")
                }
            }
            .toolbar {
                Button(role: .destructive) {
                    resetData()
                } label: {
                    Label("Reset Data", systemImage: "trash")
                }
            }
            .sheet(isPresented: $showingAddExpense) {
                AddView(expenses: expenses, currencyCode: currencyCode)
            }
        }
    }
    
    private func resetData() {
        expenses.items.removeAll()                        // clears UI
        UserDefaults.standard.removeObject(forKey: "Items") // clears storage
    }

    // MARK: - Row builder
    @ViewBuilder
    private func itemRow(_ item: ExpenseItem) -> some View {
        HStack {
            VStack(alignment: .leading) {
                Text(item.name)
                    .font(.headline)
                Text(item.type)
                    .foregroundStyle(.secondary)
            }
            Spacer()
            Text(item.amount, format: .currency(code: currencyCode))
                .foregroundStyle(color(for: item.amount))   // ⬅️ band-based style
        }
    }

    // MARK: - Helpers
    private func color(for amount: Double) -> Color {
        switch amount {
        case ..<10:   return .green      // <$10
        case ..<100:  return .orange     // $10–$99.99
        default:      return .red        // ≥$100
        }
    }

    /// Deletes the selected offsets in the *filtered* array from the master array.
    private func delete(_ sectionItems: [ExpenseItem], at offsets: IndexSet) {
        for offset in offsets {
            let item = sectionItems[offset]
            if let realIndex = expenses.items.firstIndex(where: { $0.id == item.id }) {
                expenses.items.remove(at: realIndex)
            }
        }
    }
}

// MARK: - Add-expense sheet
struct AddView: View {
    @Environment(\.dismiss) var dismiss

    @State private var name = ""
    @State private var type = "Personal"
    @State private var amount = 0.0

    var expenses: Expenses
    let currencyCode: String

    let types = ["Business", "Personal"]

    var body: some View {
        NavigationStack {
            Form {
                TextField("Name", text: $name)

                Picker("Type", selection: $type) {
                    ForEach(types, id: \.self, content: Text.init)
                }

                TextField("Amount",
                          value: $amount,
                          format: .currency(code: currencyCode))
                    .keyboardType(.decimalPad)
            }
            .navigationTitle("Add New Expense")
            .toolbar {
                Button("Save") {
                    let item = ExpenseItem(name: name, type: type, amount: amount)
                    expenses.items.append(item)
                    dismiss()
                }
                .disabled(name.isEmpty)
            }
        }
    }
}

// MARK: - Preview
#Preview { ContentView() }
```
# Project 8 Moonshot: View the history of past moon landings

# Video:[▶️ Watch Demo Video on YouTube](https://youtube.com/shorts/Itu2ka4_y6I?feature=share)

# Code: Due to the increasing complexity of the code structure, codes are stored in the main folder for you to find.
