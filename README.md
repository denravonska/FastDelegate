# About
A modified version of Don Clugston's [FastDelegate](https://www.codeproject.com/Articles/7150/Member-Function-Pointers-and-the-Fastest-Possible)  library aimed at more modern compilers. It is similar to `std::function` and `std::bind`. 

# Todo
 - Fix assignment operator ambiguity, prevents `Demo.cpp` from building.
 - Add suport for move operators

# Changes and usage

A number of quality of life changes have been done to make the library easier to read and use when used in a more modern compiler with C++11 support.

 - Variadic templates
 - Conditional invokes
 - Smaller source code, faster build times
 - Disable obsolete typedefs, see [stm32plus/issues/3](https://github.com/andysworkshop/stm32plus/issues/3)

Example:

```cpp
#include <fastdelegate/FastDelegate.h>

// A class which is capable of notifying a listener using callbacks.
class Button {
public:
   enum State {
      CLICKED,
      RELEASED
   };
   
   // Define a callback type for readability
   typedef fastdelegate::FastDelegate<void(State)> StateCb;

   void setStateCb(StateCb cb) {
      _stateCb = cb;
   }
   
private:
   // Triggered by something external
   void handleMouseClick(bool clicked) {
      // Notify listener, if any
      _stateCb.invoke_if(clicked ? CLICKED : RELEASED);
      
      // Alternatively, if the callback is known to always be assigned.
      // Note that if the callback _isn't_ assigned this will be a NULL
      // pointer call. The upside is that it avoids the conditional
      // branching that invoke_if adds.
      _stateCb(clicked ? CLICKED : RELEASED);
   }

   StateCb _stateCb;
};

// A class which is able to receive callback calls
class View {
public:
   void addButton(Button& button) {
      // Assign new callback
      button.setStateCb(
         Button::StateCb(this, &View::handleButtonClick));
   }
   
private:
   void handleButtonState(State state) {
   }
};
```

# Credit
Don Clugston
