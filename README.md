# counters
This is a piece of UI to borrow. 16 counters you can write things to with Windows native messaging interface. 

The interface is ancient but, on the plus side, it's universally availabe across languages.

![screenshot](/screenshot.png "GUI")

It's meant to assist in debugging. When you send messages from the debugged program, the counters handle them synchronously. It slows down the sending program a bit but you still can watch the data while it runs in real-time.

## Interface

It uses native Windows messaging for communication with all its limitations and advantages. It is language invariant, but I'll show all the examples in C.

To send an integer number to some counter, use this:

    SendMessage((HWND)655890, WM_USER, i, n);

Here `i` is a counter index and `n` is the number you want to see in it. 655890 - is the handle of a counter's window. It is written on top of it, and it unique for every instance. It's also accessible via the clipboard. Just click the "W" button to copy the handler, or "C" button to copy a whole SendMessage string.

To increment a counter use this:

    SendMessage((HWND)655890, WM_USER, 'I', i);

Note that 'I' works like a command here, so the counter index is now the second argument.

Decrement a counter with this:

    SendMessage((HWND)655890, WM_USER, 'D', i);

And nullify counter with this:

    SendMessage((HWND)655890, WM_USER, '0', i);
    
Please note, that `0` here is a character, not a number. Sending 0 would just set 0-counter to `i`.

You can read a value from the counter back, although, only if it has been set with messages, not by hand. And it affects performance, so it's generally best not to rely heavily on it.

    v = SendMessage((HWND)655890, WM_USER, 'R', i);

You can also use a primitive time counter:

    SendMessage((HWND)655890, WM_USER, 'T', i);
    ... some workflow long enough to be measured in process quant time slices...
    SendMessage((HWND)655890, WM_USER, 'S', i);

Sending `T` starts the corresponding timer. Sending `S` stops it and displays elapsed time in milliseconds. 

At this point, you might be wondering, what the `button` button for? It is actually a button you can "outsource" from the debugged program. It works like this. When you send a `B` command,

    b = SendMessage((HWND)655890, WM_USER, 'B', 0);

counters return 1 or more iff a `button` was pressed, and then reset its state. This way you can use it as some kind of a trigger in your debug session. I know, it looks a bit silly, but it actually helps a lot.

## GUI

* `[0]` button nullifies all counters.
* `[N]` button updates counters when `Realtime print` is off.
* `[W]` button copies handler number to the clipboard.
* `[C]` button copies C SendMessage template to the clipboard.
* `[F]` button saves counters to a file.
* `[v] Realtime print` is a checkbox which sets automatic counter updates on and off. It is convenient to have it always `on`, but is also affects performance, so this is optional.
* `[button]` button increments the inner counter when you press it so you can read it afterward.
