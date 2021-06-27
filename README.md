**Aim- To display text on SSD1306 with Word Wrapping and Scrolling**

-> String Inputs have been initialised to make debugging easier

-> String Initialisation causes the Arduino Uno to run out of SRAM

-> Storing it in PROGMEM and reading it from there saves a lot of space


**1) Word Wrapping**
       
 String is initialised as a char array and is parsed through to find any instance wherein a words length will not fit into the remaining available characters of the 21 character display. A '\n' is added in such a case for a line break. 
 
 https://wokwi.com/arduino/projects/302134252178244106
			 
			 
**2) Static Scrolling**

   After Word Wrapping is done, the char array now has word wrapped text in it, meaning that it is arranged in proper lines. I am declaring a 2D char array wherein [line number][character index for that line].
 Then we traversed through the array adding characters to array[0][i] until '\n' was found(signifying the end of the line), then line number variable is incremented to start adding characters to array [1][i] and so on.
 Once that is completed the 2D array now has the text in a line by line format( entire line is present in every location of array[line number][]). The number of times the printing function has to loop is equal to the number of lines in the paragraph(after word wrapping).
Line 1 to 8 is printed first,delay then Line 2 to 9 and so on with each new line starting at (0,0), this goes on until the last line is also printed at (0,0)
Screen has to be cleared after every printing iteration.

 https://wokwi.com/arduino/projects/302412853151793672

**3) Vertical Scrolling**

   Pixel by Pixel printing is done instead of using 2D arrays to store the string in.
    After the word wrapped string is stored in char[s], the first 8 lines(or lesser) is printed. If the number of lines >8 i.e text is bigger than what the display can hold, vertical scrolling is performed pixel wise by altering cursor position.
		
Each line is 8 pixels long, hence upper=number of lines x 8 is  done and multiplied by -1, so that we can find the position at which y-cordinate of cursor needs to be so that the last line of text is printed at (0,0)
	The loop prints pixel by pixel (in the upwards direction) thus giving a scrolling effect.
	
https://wokwi.com/arduino/projects/302508330504421897
	
**4) Memory Efficient Vertical Scrolling(Using PROGMEM)**
	
In all the previous codes, due to String being initialised or character array being formed, the SRAM is filled up by the local variables which leaves little memory for the programs to execute.
	This was solved by adding the "attrs": {"__fakeRamSize": "6500"}__ in the diagram.json of the Wokwi Simulator, so that the programs can be tested out without memory throttling being an issue. 
In this version the string is initialsed and stored in PROGRAM MEMORY,wherein it is a constant and can only be read from. 
	A buffer string, char s[350] is declared so that wrapping and scrolling functions can be performed, this is needed since we can only read from PROGMEM and wrapping function needs writing of data to take place(to get the wrapped text).
	
  This program frees up SRAM massively:-
	
  *Global variables use 1319 bytes (64%) of dynamic memory, leaving 729 bytes for local variables. Maximum is 2048 bytes.*
	
  **VS**
	
*Global variables use 519 bytes (25%) of dynamic memory, leaving 1529 bytes for local variables. Maximum is 2048 bytes.*

There still is memory limitation with only a character array of Maximum [350] being allowed before memory runs out.

https://wokwi.com/arduino/projects/302535234199486986




The simulations were done on https://wokwi.com/ and Proteus, but on Proteus SRAM Memory was an issue with bigger texts not working.


**5) Software Serial**

Software Serial Library is included and RX and TX pins are set. Stream of characters being sent over from BLE device are read and stored into char array[], and then normal wrapping and scrolling functions are performed.
