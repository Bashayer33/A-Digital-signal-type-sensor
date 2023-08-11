# A-digital-signal-type-sensor
Design and programming of an electronic circuit for a digital signal type sensor

# LED
LED project by Arduion

## Arduino – Turn LED ON and OFF With Button
In this Arduino tutorial I will show you how to turn an LED on and off with a push button. In fact, we’ll do 2 slightly different applications.
First, we will power on the LED when the button is pressed, and power off the LED when the button is not pressed.
And then we’ll modify the program to toggle the LED’s state only when we release the button.

### Arduino circuit with an LED and a button
To build the circuit you will need those components:

- Arduino board (any board, if you don’t have Uno you can easily adapt by finding corresponding pins).
- Breadboard.
- LED – any color.
- Push button.
- 220 Ohm resistor for the LED. If you don’t have this specific value, any resistor from 330 to 1k Ohm will do.
- 10k Ohm resistor for the push button. If you don’t have, you can go until 20k-50k Ohm.
- A bunch of male to male wires (including if possible black, red, and other colors).

#### Here’s the circuit you have to make

![arduino_led_push_button-1536x757](https://github.com/AmmarBahhah10/LED/assets/138979216/9e726432-8147-4654-b9cf-c1e5e62e2cb9)


##### Step by step instructions to build the circuit
- First, make sure to power off your Arduino – remove any USB cable.
- Plug a black wire between the blue line of the breadboard and a ground (GND) pin on the Arduino board.
- Plug the LED. You can notice that the LED has a leg shorter than the other. Plug this shorter leg to the ground (blue line here) of the circuit.
- Connect the longer leg of the LED to a digital pin (here pin no 8, you can change it). Add a 220 Ohm resistor in between to limit the current going through the LED.
- Add the push button to the breadboard, like in the picture.
- Connect one leg of the button to the ground, and put a 10k Ohm resistor in between. This resistor will act as a “pull down” resistor, which means that the default button’s state will be LOW.
- Add a red wire between another leg of the button and VCC (5V).
- Finally, connect a leg of the button (same side as the pull down resistor) to a digital pin (here 7).
All right your circuit is now finished. You can start writing code.

###### Turn on the LED when button is pressed, turn it off otherwise
What we want to achieve is simple: when the button is not pressed, the LED is off. And when we press the button the LED should be on.

###### The code 
#define LED_PIN 8
#define BUTTON_PIN 7

void setup() {
  pinMode(LED_PIN, OUTPUT);
  pinMode(BUTTON_PIN, INPUT);
}

void loop() {
  if (digitalRead(BUTTON_PIN) == HIGH) {
    digitalWrite(LED_PIN, HIGH);
  }
  else {
    digitalWrite(LED_PIN, LOW);
  }
}

Let’s break this code down line by line.
Setup
#define LED_PIN 8
#define BUTTON_PIN 7
First, as a best practice, we use some defines to keep the pin number for the LED and push button. That way, if you have used different pins than I, you just need to modify those 2 lines. Also, in the future if you want to change the LED from pin 8 to pin 11 for example, you can modify this line without touching anything else in the code.
void setup() {
  pinMode(LED_PIN, OUTPUT);
  pinMode(BUTTON_PIN, INPUT);
}
The setup function is executed once at the beginning of the program. This is the perfect time to initialize our pins with the pinMode() function:

- OUTPUT for the LED, as we’re going to write data to it.
- INPUT for the push button, as we’re going to read data from it.
Now, the digital pins are correctly set up.
###### Loop – Turn on the LED when button is pressed
void loop() {
  if (digitalRead(BUTTON_PIN) == HIGH) {
    digitalWrite(LED_PIN, HIGH);
  }
  else {
    digitalWrite(LED_PIN, LOW);
  }
}

In the loop function, we start by reading the button’s state with the digitalRead() function. As we have a pull down resistor on the button, we know that the non-pressed state will give us the value LOW.

(Note: if you were using a pull up resistor, or no resistor at all – with the INPUT_PULLUP  option for pinMode – this would be the opposite. HIGH when the button is not pressed, and LOW when it’s pressed.)

So, once we get the button’s state, we check if it’s HIGH or LOW:

- HIGH (pressed): we power on the LED with digitalWrite() and the HIGH state.
- LOW (not pressed): we power off the LED with digitalWrite() and the LOW state.

OK, now let’s do something a little bit more complex.

###### Toggle LED’s state with the push button – first iteration
What we want to do is to toggle the LED’s state when you press + release the button. So, the first time you release the button, the LED will turn on. The second time, it will turn off. Etc.

The code
#define LED_PIN 8
#define BUTTON_PIN 7
byte lastButtonState = LOW;
byte ledState = LOW;
void setup() {
  pinMode(LED_PIN, OUTPUT);
  pinMode(BUTTON_PIN, INPUT);
}
void loop() {
  byte buttonState = digitalRead(BUTTON_PIN);
  if (buttonState != lastButtonState) {
    lastButtonState = buttonState;
    if (buttonState == LOW) {
      ledState = (ledState == HIGH) ? LOW: HIGH;
      digitalWrite(LED_PIN, ledState);
    }
  }
}

Let’s analyze the code.

Setup
#define LED_PIN 8
#define BUTTON_PIN 7
Those defines are the same as before.

byte lastButtonState = LOW;
byte ledState = LOW;
We need to create 2 global variables, to keep the state of the button and the LED. In the loop we’ll use those variables to compare the previous state to the new state.

void setup() {
  pinMode(LED_PIN, OUTPUT);
  pinMode(BUTTON_PIN, INPUT);
}

The void setup stays the same. We just set the mode for both pins.

And now the serious stuff is coming. We enter the void loop function.

###### Monitor the button’s state
void loop() {
  byte buttonState = digitalRead(BUTTON_PIN);
  
The first thing we do is to read the button’s state and to store it inside a new local variable.

  if (buttonState != lastButtonState) {
  
Then we can compare the current state to the last one (from the previous execution of the loop function). 4 possibilities here:

LOW -> LOW (last -> current).
LOW -> HIGH.
HIGH -> LOW.
HIGH -> HIGH.

With the condition, we only enter the next block of code if the current and last state are different. If the 2 states are the same, then we don’t enter the if and the loop function is finished for this turn.

    lastButtonState = buttonState;
    
Also, now that we have the information we want, we directly store the current state into the last state variable, so that we have the correct value for the next time we enter the loop.

    if (buttonState == LOW) {
    
At this point we have only 2 possibilities left:

- Either the previous state was LOW and the current state is HIGH (not pressed to pressed).
- Or the previous state was HIGH and the current state is LOW (pressed to not pressed).

We are only interested in the second option, so here we just have to check if the current button’s state is LOW. If that’s the case, we can now turn the LED on or off.

###### Toggle the LED when the button has been released
      ledState = (ledState == HIGH) ? LOW : HIGH;
      
Here we toggle the state of the LED. I’m not a big fan of one-liners but this one is really handful when you just need to toggle a state. This will save you 3-4 lines of code for something really trivial.

Here’s the template for this one-liner: (condition to evaluate) ? if true use this value : if false use this value.

So, if the LED is currently powered on (ledState == HIGH), we assign the value LOW. If the LED is powered off (ledState != HIGH), we assign the value HIGH.

      digitalWrite(LED_PIN, ledState);
    }
  }
}

And the last thing we need to do is of course to physically set the state for the LED, with the new computed state.

###### Turn LED on and off with button – using debounce
One thing you might notice with the previous experiment: sometimes when you press and release the button, the LED’s state doesn’t change, or blinks quickly multiple times.

This is because the button is physically bouncing when you press it. Thus, many false positives will be interpreted by the Arduino. What you can do to prevent that is to add a debounce delay in your code. For example, you can decide that when the program detects a change in the button’s state, it will wait 50 milliseconds before considering another change viable.

Let’s add a few lines to our previous code.

###### The improved code

#define LED_PIN 8
#define BUTTON_PIN 7
byte lastButtonState = LOW;
byte ledState = LOW;
unsigned long debounceDuration = 50; // millis
unsigned long lastTimeButtonStateChanged = 0;
void setup() {
  pinMode(LED_PIN, OUTPUT);
  pinMode(BUTTON_PIN, INPUT);
}
void loop() {
  if (millis() - lastTimeButtonStateChanged > debounceDuration) {
    byte buttonState = digitalRead(BUTTON_PIN);
    if (buttonState != lastButtonState) {
      lastTimeButtonStateChanged = millis();
      lastButtonState = buttonState;
      if (buttonState == LOW) {
        ledState = (ledState == HIGH) ? LOW: HIGH;
        digitalWrite(LED_PIN, ledState);
      }
    }
  }
}

Now, if you run this program, you won’t have any problem with the LED’s state being changed quite randomly when you press/release the button.

And here is how we achieve that.

###### Debounce explained
unsigned long debounceDuration = 50; // millis
unsigned long lastTimeButtonStateChanged = 0;

We create 2 new global variables:

- One to decide how long we want to wait before validating a second change in the button’s state. Here it means that if the state changes, any changes after that in the next 50 milliseconds will be ignored. 50 millis is a good value, you can experiment with lower and upper values to check the behavior.
- Another one to keep the last time the state was changed, so we have a starting point to compare to.

void loop() {
  if (millis() - lastTimeButtonStateChanged > debounceDuration) {
  
Then, in the loop, we only start the button/LED functionality if enough time has passed since the last time the button’s state was changed.

To do that it’s quite simple: we compare the duration since the last update time to the required debounce duration.

    if (buttonState != lastButtonState) {
      lastTimeButtonStateChanged = millis();
      
Once the debounce delay has been passed, then we can do what we did before. And don’t forget to update the starting point for the “debounce timer” just after you detect a change in the button’s state. So, the next time the program enters the loop, it will wait for 50 milliseconds (or the value you’ve chosen) to detect new changes in the button’s state.

###### Conclusion – Arduino turn Led ON and OFF with button
In this tutorial you have seen how to build an Arduino circuit with an LED and a push button, and also how to control this circuit to turn the LED on and off with the button.

Even if it’s a rather simple application, there are many ways to program it. You can power on the LED only when the button is pressed, or make the LED blink when you release the button, and so on.

Another way to write the code (for the exact same functionalities) is to use Arduino interrupts – available for the boards having interrupt pins.

To go further from here, check out:

Control multiple LEDs with a push button.
Arduino program with LED, push button, and potentiometer.
