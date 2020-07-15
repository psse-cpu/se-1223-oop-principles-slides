Separate presentation from (business) logic
-------------------------------------------



### What's wrong with this code?

```dart
class Patient {
  double currentTemperature;

  Patient(this.currentTemperature) {}

  void checkFever() {
    if(currentTemperature < 37.5) {
      print('✔ Safe');
    } else {
      print('❌ #stayAtHome');
    }
  }
}
```

It only works in the CLI! <!-- .element class="fragment" -->

<span class="fragment">
  <code>print</code> does produce output on mobile or web apps.
</span>



### Let's just change the class when the GUI is needed (1/2)

* Flaws of this reasoning:
  - When you touch working code, you're likely to break it.
    + One tiny typo during your modification, and boom!
    + SE economics won't allow you to test everything
  - You assume there's only one UI
    + Many apps are available on desktop, mobile, and Web
      * Messenger, Zoom, Gmail, browsers, Viber, MS Office, Shopee
    + Their business logic is the same regardless of your device
    + but layouts are different, some devices might even have added or missing functionality
      * Messenger on phones: chat heads, send SMS, free or data mode
    + i18n and l10n!  (translations! --- 
      <span style="font-size: 0.75em"><i>Exercise: try FB or Google in Tagalog 😁</i></span>)



### Let's just change the class when the GUI is needed (2/2)

* Flaws of this reasoning, cont'd:
  - UI is less stable: some apps tweak the UI/UX over time
    + improve navigation, layout, intuitiveness, convenience
    + What if Zoom changes from 49 faces on screen to 64?  Business logic (online meetings) is still 
      the same
  - Some apps change UI frameworks or libraries over time
    + Google enough and you'll read tech companies rewriting the UI in a different framework or
      library.
    + What if Flutter 2 is not backwards-compatible with Flutter 1?
      * Google has done with with Angular 1 and Angular 2+
  - Unit tests are harder with CLI output
    + they're automated tests for a single unit like classes (SE 2124)



### SRP: Single Responsibility Principle

Another principle 😱😱😱!

* Restating the 3-word phrase: Each class should only have one responsibility
* The most popular way of saying this principle:

  > Each class should only have one reason to change.

* Single responsibility does not mean single method



### What are the responsibilites of the `Patient` class?

+ What are the reasons to change the Patient class?
  * business logic: model a real-world patient
    - hey heatstrokes can raise body temperature to as much as 40°C
    - heatstrokes and exercise-releated heat exhaustion are not fever!
    - DOH suggests to fix the Patient class to handle these
  * presentation:  it prints **✔ Safe** or **❌ #stayAtHome**
    - what if YES or NO?
    - what about for non-English speakers in the PH?  
      + `✔ Ligtas` and `❌ #manatiliSaBahay` (cringe)
    - mobile apps anyone?
  


### How to separate UI from logic?

+ You already know how to do that.  
  - don't `print`, just `return`

```dart [1-9 | 11-15]
class Patient {
  double currentTemperature;

  Patient(this.currentTemperature) {}

  bool get hasFever {
    return currentTemperature >= 37.5;
  }
}

// main.dart
void main() {
  final patient = Patient(37.5);
  print(patient.hasFever ? '❌ #stayAtHome' : '✔ Safe');
}
```



### What about input?

+ You already know how to do that.  
  - those become instance vars
  - or arguments to methods and constructors

```dart
import 'dart:io';

void main() {
  stdout.write('Current temperature: ');
  final temperature = double.parse(stdin.readLineSync());

  final patient = Patient(temperature);

  print('Temperature: ${patient.currentTemperature}°C');
  print("Has fever? ${patient.hasFever ? 'YES' : 'NO'}");
  print(patient.hasFever ? '❌ #stayAtHome' : '✔ #staySafe');
}
```


## Flutter Demo

In the lecture video recording, no code in slides

* Things to note:
  - If the business logic does not change, the Patient class should not change
  - refactoring does not count
  - we can make different UIs **without ever touching** the Patient class
    + we can reuse `Patient` in different UIs!
    + yes reusable code doesn't only apply to libraries
* this principle promotes [**separation of concerns**](https://en.wikipedia.org/wiki/Separation_of_concerns)