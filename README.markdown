# sphero

* http://github.com/estryker/sphero

[![Build Status](https://travis-ci.org/estryker/sphero.png?branch=master)](https://travis-ci.org/estryker/sphero)

## DESCRIPTION:

A ruby gem for controlling your Sphero ball.  Sends commands over the TTY
provided by the bluetooth connection.

## FEATURES:

* An easy to use DSL that is friendly for new coders. Simple commands for moving the ball forward, turning, and changing colors. 
  More advanced usage includes collision detection and a new keyboard control mode! 

## SYNOPSIS:

You can easily start your Sphero and send it commands like this:

```ruby
require "sphero"

Sphero.start '/dev/tty.Sphero-BRG-AMP-SPP' do
  colors = ['red','yellow','blue','green']

  speed 70
  4.times do | i |
    color colors[i]
    forward 4
    turnright 90
  end
end
```
## Here is an example with a collision detection block
```ruby
def random_color
  Sphero::COLORS.keys.sample
end

Sphero.start '/dev/tty.Sphero-BRG-AMP-SPP' do

  # This block will run when Sphero detects that it collided with something
  on_collision do | r |
    # puts "collision detected: #{r.x} #{r.y} #{r.z} #{r.x_magnitude} #{r.y_magnitude} #{r.speed} "
    10.times do
      color random_color
      sleep 0.07
    end
    colors.rotate!
    color colors.first
    turnaround
    forward 1
  end
  
  direction 0
  speed 100
  
  color 'yellow'
  10.times do
    forward 4 
    turnright 36
  end
end

```
## Use Keyboard control to make Sphero run in interactive mode!
*Use the 'h' key to see all the registered commands for keyboard mode. Changing speed and color are currently built in.* 
```ruby
require 'sphero'

Sphero.start '/dev/tty.Sphero-BRG-AMP-SPP' do

  on_collision do | x |
    5.times do
      pick_random_color
      sleep 0.1
    end
  end

  # add a command that can be run by hitting the '/' followed by 'square X' where X determines the size of the square
  on_keyboard_command "square","draw a square of specified size"  do | size_str |
    size = size_str.to_i
    4.times do | i |
      forward size
      turnright 90
    end
  end

  on_keyboard_command "flash","flash colors the given number of times quickly" do | times_str |
    flash times_str.to_i, 0.08
  end
  
  speed 50

  puts "Ready!"
  keyboard_mode
end
```
## If you want to use the more explicit API
```ruby
Sphero.start '/dev/tty.Sphero-YBW-RN-SPP' do
	roll 60, Sphero::FORWARD
	keep_going 3

	roll 60, RIGHT
	keep_going 3

	roll 60, BACKWARD
	keep_going 3

	roll 60, LEFT
	keep_going 3

	stop
end
```

```ruby
Sphero.start "/dev/tty.Sphero-PRG-RN-SPP" do
	ping

	# Roll 0 degrees, speed 125
	roll(125, 0)

	# Turn 360 degrees, 30 degrees at a time
	0.step(360, 30) do  |h|
  	h = 0 if h == 360

		# Set the heading to h degrees
 		heading = h
 		keep_going 1
	end

	keep_going 1
	stop
end
```

Here is a another way to do the same thing as the previos example, via just normal method calls:

```ruby
s = Sphero.new "/dev/tty.Sphero-PRG-RN-SPP"
s.ping

# Roll 0 degrees, speed 125
s.roll(125, 0)

# Turn 360 degrees, 30 degrees at a time
0.step(360, 30) do |h|
  h = 0 if h == 360

  # Set the heading to h degrees
  s.heading = h
  sleep 1
end
sleep 1
s.stop
```

## Pairing sphero with ubuntu
Add your user to the `dialout` group
```
$ sudo usermod -a -G dialout <user>
```
Then logout or restart your computer. Once your user is logged back in, pair the sphero with the ubuntu bluetooth manager.

Once paired, you may now bind your sphero to a rfcomm port
```
$ sudo hcitool scan 
Scanning ...
<address>		Sphero
$ sudo rfcomm bind /dev/rfcomm0 <address> 1
```

You may now access the sphero from `/dev/rfcomm0`

## REQUIREMENTS:

* A Sphero ball connected to your computer
* Supports MRI 1.9.X, 2.X, JRuby, and Rubinius 2.0rc1 

## INSTALL:

* gem install sphero

## LICENSE:

(The MIT License)

Copyright (c) 2012 Aaron Patterson
Copyright (c) 2012-2013 The Hybrid Group
Copyright (c) 2017 Ethan Stryker

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the
'Software'), to deal in the Software without restriction, including
without limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Software, and to
permit persons to whom the Software is furnished to do so, subject to
the following conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED 'AS IS', WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY
CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT,
TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE
SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
