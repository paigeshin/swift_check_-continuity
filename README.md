# swift_check_or_get_valid_items_when_user_executes_consecutively_habit-app

### DAY BASE 

```swift 
    func validateRecordings() -> Bool {
        var lastDate: Date?

        let sortedRecordings = self.records.sorted { $0.date < $1.date }

        for i in 0..<sortedRecordings.count {
            let currentRecording = sortedRecordings[i]

            if i > 0 {
                let previousRecording = sortedRecordings[i - 1]
                let daysBetweenRecordings = Calendar.current.dateComponents([.day], from: previousRecording.date, to: currentRecording.date).day ?? 0

                if daysBetweenRecordings > 1 {
                    // Recordings are not consecutive, return false
                    return false
                }
            }

            lastDate = currentRecording.date
        }

        // All recordings are consecutive, return true
        return true
    }
    
    func validatedRecords() -> [Record] {
        var validatedRecordings: [Record] = []
        var lastValidatedDate: Date?

        let sortedRecordings = self.records.sorted { $0.date < $1.date }

        for i in 0..<sortedRecordings.count {
            let currentRecording = sortedRecordings[i]

            if i > 0 {
                let previousRecording = sortedRecordings[i - 1]
                let daysBetweenRecordings = Calendar.current.dateComponents([.day], from: previousRecording.date, to: currentRecording.date).day ?? 0

                if daysBetweenRecordings > 1 {
                    // Recordings are not consecutive, skip current recording
                    continue
                }
            }

            if let lastDate = lastValidatedDate, Calendar.current.isDate(lastDate, inSameDayAs: currentRecording.date) {
                // User already recorded within the current day, skip current recording
                continue
            }

            // Recording passed both checks, add to validated recordings
            validatedRecordings.append(currentRecording)
            lastValidatedDate = currentRecording.date
        }

        return validatedRecordings
    }
    
```

### DAY STARTS AT 18:00 AND ENDS AT 18:00 NEXT DAY

```swift
    func validateRecordings() -> Bool {
        var lastDayEnd: Date?
        var i = 0

        let sortedRecordings = self.records.sorted { $0.date < $1.date }

        while i < sortedRecordings.count {
            let currentRecording = sortedRecordings[i]

            // Calculate the start and end of the current day
            let currentDayStart = Calendar.current.date(bySettingHour: 18, minute: 0, second: 0, of: currentRecording.date)!
            let currentDayEnd = Calendar.current.date(bySettingHour: 18, minute: 0, second: 0, of: currentRecording.date.addingTimeInterval(86400))!

            if lastDayEnd == nil || currentDayStart == lastDayEnd {
                // First recording of the day or consecutive recording
                lastDayEnd = currentDayEnd
            } else {
                // Recording is not consecutive, return false
                return false
            }

            // Skip any other recordings within the same day
            repeat {
                i += 1
            } while i < sortedRecordings.count && sortedRecordings[i].date < currentDayEnd
        }

        // All recordings are consecutive, return true
        return true
    }

    func validatedRecords() -> [Record] {
        var lastDayEnd: Date?
        var validRecordings: [Record] = []
        var i = 0

        let sortedRecordings = self.records.sorted { $0.date < $1.date }

        while i < sortedRecordings.count {
            let currentRecording = sortedRecordings[i]

            // Calculate the start and end of the current day
            let currentDayStart = Calendar.current.date(bySettingHour: 18, minute: 0, second: 0, of: currentRecording.date)!
            let currentDayEnd = Calendar.current.date(bySettingHour: 18, minute: 0, second: 0, of: currentRecording.date.addingTimeInterval(86400))!

            if lastDayEnd == nil || currentDayStart == lastDayEnd {
                // First recording of the day or consecutive recording
                lastDayEnd = currentDayEnd
                validRecordings.append(currentRecording)
            } else {
                // Recording is not consecutive, skip it
            }

            // Skip any other recordings within the same day
            repeat {
                i += 1
            } while i < sortedRecordings.count && sortedRecordings[i].date < currentDayEnd
        }

        // Return array of valid recordings
        return validRecordings
    }
```
