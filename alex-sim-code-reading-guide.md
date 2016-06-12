# Simple Guide to Alex Simulator Code Reading

### Source Code

[alex.js](https://github.com/paulzfm/v9.js/blob/master/assets/js/alex.js).

### Preparations

We suggest you to understand the following basic ideas we use on the implementation of the simulator:

- Closures in javascript: what is closure, how to implement closures, and the variable scope rules. [reference](http://www.w3schools.com/js/js_function_closures.asp).
- Middleware (design pattern?) like in node.js. We implement one on line 230. [reference](http://expressjs.com/en/guide/using-middleware.html).
- How to simulate int32 and uint32 in js? Use '>>> 0' to cast signed to unsigned and '<< 0' to cast unsigned to signed.
- The strange behavior of infix operator priorities in js. [reference](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Operator_Precedence).

### Reading Guide

The CPU is executed cycle by cycle, for each cycle, either one of the following operations may happen:

```javascript
var hdlrFatal; // fatal error, exit process
var hdlrExcpt; // handle exception
var hdlrItrpt; // handle interrupt
var hdlrFixsp;
var hdlrChkpc; // go on fetch instruction / hdlrFixpc
var hdlrFixpc;
var hdlrChkio;
var hdlrInstr; // fetch instruction
```

On line 1588, function `runNonDebug` schedule the execution of CPU.

When it is time to execute some instruction, on line 1276, the target executor is called, based on the opcode.

From line 643, executors for each instruction is registered. We divide the execution for each instruction into the following steps:

1. Decode: either decode R-type or I-type, and extend the immediate number either on signed, unsigned, or offset.
2. EXE: execute the computations, e.g., ALU.
3. Other steps due to different specific instruction.
4. Continue to next instruction or jump to some address (for Jump/Branch instructions).

These steps are so-called "pipeline" in our implementation, we assemble these little steps into a large one so that an instruction can be executed. We divide them into such little steps to reuse most of the code, noticing that most instruction do the exact same thing except a signal step.

All the helper functions from line 281 are used to help us implement the execution logic for each instruction.

Comments are useful, you should read them.

### Debug Advise

- Google chrome debug tool is your friend.
- Use `console.log` to see how things work. Note we also support a disassembler so that you can read the machine instruction easier.
