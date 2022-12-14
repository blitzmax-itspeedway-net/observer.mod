# Module bmx.observer

An event observer system easily added to any existing type.

**VERSION:** 1.6

# DEPENDENCIES
* [BlitzMax-NG](https://blitzmax.org/downloads/)

# MANUAL INSTALL USING GIT
**LINUX:**
```
    # mkdir -p ~/BlitzMax/mod/bmx.mod
    # cd ~/BlitzMax/mod/bmx.mod
    # git clone https://github.com/blitzmax-itspeedway-net/observer.mod.git
    # cd observer.mod
    # chmod +x compile.sh
    # ./compile.sh
```
**WINDOWS:**
```
    C:\> mkdir C:\BlitzMax\mod\bmx.mod
    C:\> cd /d C:\BlitzMax\mod\bmx.mod
    C:\> git clone https://github.com/blitzmax-itspeedway-net/observer.mod.git
    C:\> cd observer.mod
    C:\> compile.bat
```

# MANUAL INSTALL USING ZIP
* Create a folder in your BlitzMax/mod folder called "bmx.mod"
* Download ZIP file from GitHub and unzip it: You will have a folder called observer.mod.
* Copy folder observer.mod-main/observer.mod to BlitzMax/mod/bmx.mod/
* Run the compile.sh or compile.bat file located in the lexer.mod folder to compile

# UPDATE USING GIT
**LINUX:**
```
    # cd ~/BlitzMax/mod/bmx.mod/observer.mod
    # git pull
    # chmod +x compile.sh
    # ./compile.sh
```
**WINDOWS:**
```
    C:\> cd /d C:\BlitzMax\mod\bmx.mod\observer.mod
    C:\> git pull
    C:\> compile.bat
```

# Importing the module
```
import bmx.observer
```

# Using the Module

## Adding a custom event
```
Global EVENT_EXPLOSION:int = Observer.Allocate( "Explosion" )
```

## Getting an event name
```
Local eventName:String = Observer.name( EVENT_EXPLOSION )
```

## Posting an event
```
Observer.post( EVENT_EXPLOSION, data )
```

## Listening for events
An event listener can be added to any existing type, simply by adding an `Implements IObserver` to the definition and defining Method Observe().
```
Type MyType Implements IObserver

    Method Observe( event:Int, data:Object )
        Debuglog( "MyType:Observe(): "+Observer.name( event ) )
    End Method

End Type
```

## Adding and Removing event listeners
The .on() method is used to add a listener. Events are then delivered to the Observe() method:
The .off() method is used to remove a listener:
```
Type MyType Implements IObserver

    Method Observe( event:Int, data:Object )
        Debuglog( "MyType:Observe(): "+Observer.name( event ) )
    End Method

    Method Enable()
        Observer.on( EVENT_EXPLOSION, self )
    End Method

    Method Disable()
        Observer.off( EVENT_EXPLOSION, self )
    End Method

End Type
```

## Obtain number of listeners
You can obtain the number of handlers configured for a specific event:
```
Local handlers:int = Observer.count( EVENT_EXPLOSION )
```

## Debugging messages
To provide the ability to log or debug messages, the method debug() has been included that
will post all messages to your Observe() method:

```
Type MyType Implements IObserver

    Method New()
        Observer.debug( self )
    End Method

End Type
```

## System Event Support
```
Observer.Events( True|False )
```
Adding support for system events will allow you to subscribe to all the standard Blitzmax events.
```
Observer.Events( True )     ' Enable BlitzMax events

Type MyType Implements IObserver

    Method New()
        Observer.on( EVENT_APPSUSPEND, self )
        Observer.on( EVENT_APPRESUME, self )
    End Method

    Method Observe( event:Int, data:Object )
        Select event
        Case EVENT_APPSUSPEND
            game.pause()
        Case EVENT_APPRESUME
            game.pause( False )
        End Select
    End Method

End Type
```

Available BlitzMAX events include the following:
(Some of these are MaxGUI Specific).

```
EVENT_APPMASK          EVENT_APPSUSPEND     EVENT_APPRESUME
EVENT_APPTERMINATE     EVENT_APPOPENFILE    EVENT_APPIDLE
EVENT_KEYMASK          EVENT_KEYDOWN        EVENT_KEYUP
EVENT_KEYCHAR          EVENT_KEYREPEAT      EVENT_MOUSEMASK
EVENT_MOUSEDOWN        EVENT_MOUSEUP        EVENT_MOUSEMOVE
EVENT_MOUSEWHEEL       EVENT_MOUSEENTER     EVENT_MOUSELEAVE
EVENT_TIMERMASK        EVENT_TIMERTICK      EVENT_HOTKEYMASK
EVENT_HOTKEYHIT        EVENT_GADGETMASK     EVENT_GADGETACTION
EVENT_GADGETPAINT      EVENT_GADGETSELECT   EVENT_GADGETMENU
EVENT_GADGETOPEN       EVENT_GADGETCLOSE    EVENT_GADGETDONE
EVENT_GADGETLOSTFOCUS  EVENT_GADGETSHAPE    EVENT_WINDOWMASK
EVENT_WINDOWMOVE       EVENT_WINDOWSIZE     EVENT_WINDOWCLOSE
EVENT_WINDOWACTIVATE   EVENT_WINDOWACCEPT   EVENT_WINDOWMINIMIZE
EVENT_WINDOWMAXIMIZE   EVENT_WINDOWRESTORE  EVENT_MENUMASK
EVENT_MENUACTION       EVENT_STREAMMASK     EVENT_STREAMEOF
EVENT_STREAMAVAIL      EVENT_PROCESSMASK    EVENT_PROCESSEXIT
EVENT_TOUCHMASK        EVENT_TOUCHDOWN      EVENT_TOUCHUP
EVENT_TOUCHMOVE        EVENT_MULTIGESTURE   EVENT_USEREVENTMASK
```

## THREADING
Locking/Unlocking mutexes in a non-threaded application was considered wasteful
and therefore Observer is not threadsafe by default. It was decided to make 
threads optional and a method was added in version 1.4 to enable or disable 
threasafe mode.

	Observer.threaded()			' Enable threadsafe mode
	Observer.threaded( False )	' Disable threadsafe mode (DEFAULT)

## MAXGUI
Instead of using an event loop; you can use the Observer with MaxGUI by replacing the mainloop with a simple wait:
```
Repeat
	WaitEvent()		' Process MaxGUI events
	Delay(5)
Forever
```
An full example is included in the examples folder 

## Flip Event Support
```
Observer.Flip( True|False )
```
Adding support for flip events will allow you to subscribe to the `EVENT_FLIP` event.

```
Observer.Flip( True )     ' Enable Flip events

Type MyType Implements IObserver

    Method New()
        Observer.on( EVENT_FLIP, self )
    End Method

    Method Observe( event:Int, data:Object )
        Select event
        Case EVENT_FLIP
            gui.render()
        End Select
    End Method

End Type
```

