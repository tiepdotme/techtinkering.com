The only Jupiter Ace emulator that I found, which would work under Linux, was written by Edward Patel and is called <a href="http://hem.passagen.se/tiletech/ace.htm">xace</a>.  There is also a Windows version available, but I don't know anything more about that.  The following instructions, taken partially from the site's [help instructions](http://hem.passagen.se/tiletech/ace.txt)
, will show how to install it under Linux.

1.  Download the tarball: <a href="http://hem.passagen.se/tiletech/xace-0.4.tar.gz">xace-0.4.tar.gz</a>.
2.  Extract the files:
    ```` bash
    $ tar -xvzf xace-0.4.tar.gz
    ````
3.  Change into the extracted directory:
    ```` bash
    $ cd xace-0.4
    ````
4.  Run xmkmf to create a makefile from an imake file
    ```` bash
    $ xmkmf
    ````
5.  I have created a patch which is an amalgamation of several patches which I found on the internet.  Each had problems, which I have fixed.  The patch can be downloaded from <a href="/downloads/xace-0.4.patch">here</a>.  Save it as `xace-0.4.patch` in the same directory as the xAce source files.
6.  Patch `main.c` using the patch downloaded above:
    ```` bash
    $  patch xmain.c xace-0.4.patch
    ````

7.  Make the project:
    ```` bash
    $ make
    ````

8.  You can then run the emulator using:
    ```` bash
    $ ./xace
    ````

## A Quick Test
When entering any of the following definitions, please make sure that you enter them exactly with the correct placement of spaces.

Try entering the following to create a word called `star`:
```` text
: star ." *" ;
````

Now when you type `star` and press return, a star is output.

You could also create a word called `stars`:
```` text
: stars 0 do star loop cr ;
````

When you enter `stars` preceded by a number it will printer that number of stars, e.g. the following will print 4 stars:
```` text
4 stars
````

There is a problem with the `stars` definition however.  If you ask for 0 stars, you will still get one star.  There are plenty of resources to learn forth out there, so you shouldn't find it difficult working out how to correct this one.  Think of it as your first little forth programming test.

## Where Now?
There aren't many Jupiter Ace websites around, but one that is particularly good is <a href="http://www.jupiter-ace.co.uk/">The Jupiter Ace Resource Site</a>.  Another useful source of information is <a href="http://hem.passagen.se/tiletech/forth.htm">Edward Patel's crash course in forth</a>.  This is particularly useful as it mentions Jupiter Ace specific words.  I hope that you enjoy playing with this emulator and hopefully for those who haven't used forth, you will gain a new appreciation of it.
