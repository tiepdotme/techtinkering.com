There are lots of different programming challenges, but this year one in particular has caught my attention and I have decided to take part.  [Advent of Code](https://adventofcode.com) starts 1st December with daily programming puzzles throughout [advent](https://en.wikipedia.org/wiki/Advent).

To make this challenge a little more interesting I have decided to do the 25 puzzles in 25 different programming languages.  This will be interesting as while I have used quite a lot of different languages, I don't know 25 well.  I'm also have quite limited time in the run up to Christmas.  Therefore, I'm going to solve each puzzle first in Tcl and then create a second version in another language that gives the same result.  Tcl is one of my favourite languages so the code should be reasonably good.

So far I have solutions for:
* [Day 1: Chronal Calibration - Tcl](#day1)
* [Day 2: Inventory Management System - Ruby](#day2)
* [Day 3: No Matter How You Slice It - C](#day3)
* [Day 4: Repose Record - Python](#day4)
* [Day 5: Alchemical Reduction - Racket](#day5)
* [Day 6: Chronal Coordinates - Commodore 128 Basic](#day6)
* [Day 7: The Sum of Its Parts - Pascal](#day7)
* [Day 8: Memory Maneuver - F#](#day8)
* [Day 9: Marble Mania - Julia](#day9)
* [Day 10: The Stars Align - JavaScript](#day10)
* [Day 11: Chronal Charge - Pyret](#day11)

I am putting my solutions in a [repo](https://github.com/lawrencewoodman/adventofcode) on GitHub.

<a name="day1" /></a>
## Day 1: Chronal Calibration - Tcl

To ease myself in gradually the [day 1](https://adventofcode.com/2018/day/1) puzzle has been done with [Tcl](https://wiki.tcl-lang.org/) alone.

```` tcl
# Code for Day 1 of Advent of Code: https://adventofcode.com/2018/day/1
# Tcl Solution
set fp [open "day1.input" r]
set input [read $fp]
close $fp

proc part1 {input} {
  return [::tcl::mathop::+ {*}$input]
}

proc part2 {freqs startFreq input} {
  dict set freqs $startFreq 1
  set sum $startFreq
  foreach line $input {
    if {$line != ""} {
      set sum [expr {$sum + $line}]
      if {[dict exists $freqs $sum]} {
        return $sum
      } else {
        dict set freqs $sum 1
      }
    }
  }
  tailcall part2 $freqs $sum $input
}

puts "part1: [part1 $input]"
puts "part2: [part2 {} 0 $input]"
````

<a name="day2" /></a>
## Day 2: Inventory Management System - Ruby

For [day 2](https://adventofcode.com/2018/day/2) I have chosen [Ruby](https://www.ruby-lang.org).  I used to use ruby quite a lot at one time, but was surprised how much I had forgotten.  The code is probably not very idiomatic, but it wasn't too hard to convert the Tcl solution over to ruby.

```` ruby
# Code for Day 2 of Advent of Code: https://adventofcode.com/2018/day/2
# Ruby Solution
input = File.read("day2.input")

def part1(input)
  numDuplicates = Hash.new(0)
  input.each_line do |id|
    numIncreased = Hash.new(0)
    freqs = Hash.new(0)
    id.each_char { |c| freqs[c] += 1 }
    freqs.each do |c, num|
      if !numIncreased.key?(num)
        numDuplicates[num] += 1
        numIncreased[num] = 1
      end
    end
  end
  numDuplicates[2] * numDuplicates[3]
end

def part2(input)
  input.each_line do |id1|
    input.each_line do |id2|
      numDiff = 0
      commonLetters = ""
      id1.each_char.with_index do |cid1, index|
        cid2 = id2[index]
        if cid1 == cid2
          commonLetters << cid1
        else
          numDiff += 1
        end
      end
      if numDiff == 1
        return commonLetters
      end
    end
  end
end

puts "Part1: #{part1(input)}"
puts "Part2: #{part2(input)}"
````

<a name="day3" /></a>
## Day 3: No Matter How You Slice It - C

For [day 3](https://adventofcode.com/2018/day/3) I have chosen [C](https://en.wikipedia.org/wiki/C_(programming_language)).  This is a language that I've used longer than any other, but haven't touched for over 3 years, so was a bit rusty.  It was quite enjoyable getting back in the saddle after so long.  For the solution to the puzzle I have made it do just enough to solve the problem and therefore it uses static arrays and isn't very dynamic.

```` c
// Code for Day 3 of Advent of Code: https://adventofcode.com/2018/day/3
// C Solution
//
// Copyright (C) 2018 Lawrence Woodman <lwoodman@vlifesystems.com>
// Licensed under an MIT licence.  Please see LICENCE.md for details.

#include <stdio.h>
#include <stdlib.h>

#define BITMAPMAXX 1500
#define BITMAPMAXY 1500
#define CLAIMSIZE 100
#define MAXCLASHIDS 2000

int part1 () {
  int count;
  int x, y, maxX, maxY;
  int id, leftMargin, topMargin, width, height;
  char bitmap[BITMAPMAXY][BITMAPMAXX];
  FILE *fp;
  static char claim[CLAIMSIZE];

  for (x = 0; x < BITMAPMAXX; x++) {
    for (y = 0; y < BITMAPMAXY; y++) {
      bitmap[y][x] = 0;
    }
  }

  if ((fp = fopen("day3.input", "r")) == NULL) {
		exit(EXIT_FAILURE);
  }
  while (fgets(claim, CLAIMSIZE, fp)) {
    sscanf(claim, "#%d @ %d,%d: %dx%d",
         &id, &leftMargin, &topMargin, &width, &height);
    maxX = leftMargin + width;
    maxY = topMargin + height;
    if (maxX >= BITMAPMAXX || maxY >= BITMAPMAXY) {
      fprintf(stderr, "bitmap too small\n");
      exit(EXIT_FAILURE);
    }
    for (x = leftMargin + 1; x <= maxX; x++) {
      for (y = topMargin + 1; y <= maxY; y++) {
        bitmap[y][x]++;
      }
    }
  }

  fclose(fp);

  count = 0;
  for (x = 0; x < BITMAPMAXX; x++) {
    for (y = 0; y < BITMAPMAXY; y++) {
      if (bitmap[y][x] >= 2) {
        count++;
      }
    }
  }
  return count;
}

int part2 () {
  int clashIDS[MAXCLASHIDS];
  int maxID = 0;
  int x, y, maxX, maxY;
  int id, leftMargin, topMargin, width, height;
  short bitmap[BITMAPMAXY][BITMAPMAXX];
  FILE *fp;
  static char claim[CLAIMSIZE];

  for (x = 0; x < BITMAPMAXX; x++) {
    for (y = 0; y < BITMAPMAXY; y++) {
      bitmap[y][x] = 0;
    }
  }
  if ((fp = fopen("day3.input", "r")) == NULL) {
    fprintf(stderr, "Couldn't open input\n");
		exit(EXIT_FAILURE);
  }
  while (fgets(claim, CLAIMSIZE, fp)) {
    sscanf(claim, "#%d @ %d,%d: %dx%d",
         &id, &leftMargin, &topMargin, &width, &height);
    maxX = leftMargin + width;
    maxY = topMargin + height;
    if (id >= MAXCLASHIDS) {
      fprintf(stderr, "clashIDS too small\n");
      exit(EXIT_FAILURE);
    }
    for (x = leftMargin + 1; x <= maxX; x++) {
      for (y = topMargin + 1; y <= maxY; y++) {
        if (bitmap[y][x] >= 1) {
          clashIDS[id] = 1;
          maxID = id > maxID ? id : maxID;
          clashIDS[bitmap[y][x]] = 1;
        } else {
          bitmap[y][x] = id;
        }
      }
    }
  }

  fclose(fp);

  for (id = 1; id <= maxID; id++) {
    if (clashIDS[id] == 0) {
      return id;
    }
  }
  return -1;
}

void main(void) {
  printf("part1: %d\n", part1());
  printf("part2: %d\n", part2());
}
````

<a name="day4" /></a>
## Day 4: Repose Record - Python
For [day 4](https://adventofcode.com/2018/day/4) I have chosen [Python](https://www.python.org/).  This is one of the most popular programming languages and yet I have never written anything in it before today.  This made it quite interesting as I have heard lots of good things about the language and it was great to give it a go.  Apologies to any Python programmers for my naturally poor style.

```` python
# Code for Day 4 of Advent of Code: https://adventofcode.com/2018/day/4
# Python Solution
#
# Copyright (C) 2018 Lawrence Woodman <lwoodman@vlifesystems.com>
# Licensed under an MIT licence.  Please see LICENCE.md for details.

import re
from datetime import datetime, date, time

input = open('day4.input').readlines()

def getDate(e):
    stampRE = re.compile("^.*(\d\d\d\d-\d\d-\d\d \d\d:\d\d).*$")
    stamp = stampRE.findall(e)[0]
    return datetime.strptime(stamp, "%Y-%m-%d %H:%M").timestamp()

def part1(input):
    pattern = {}
    input.sort(key=getDate)

    guardRE = re.compile("^.*\d\d\d\d-\d\d-\d\d \d\d:\d\d.*Guard #(\d+).*$")
    sleepRE = re.compile("^.*\d\d\d\d-\d\d-\d\d \d\d:(\d\d).*falls.*$")
    wakeRE = re.compile("^.*\d\d\d\d-\d\d-\d\d \d\d:(\d\d).*wake.*$")
    for entry in input:
        if guardRE.match(entry):
            guardID = guardRE.findall(entry)[0]
        elif sleepRE.match(entry):
            sleep = int(sleepRE.findall(entry)[0])
        elif wakeRE.match(entry):
            wake = int(wakeRE.findall(entry)[0])
            for m in range(sleep, wake):
                num = 1
                if guardID not in pattern:
                    pattern[guardID] = {}
                if m in pattern[guardID]:
                    num = pattern[guardID][m]
                    num += 1
                pattern[guardID][m] = num

    sleepiestID = -1
    sleepiestMinutes  = -1
    for id, sleep in pattern.items():
        totalSleep = 0
        for m, num in sleep.items():
            totalSleep += num
        if totalSleep > sleepiestMinutes:
            sleepiestMinutes = totalSleep
            sleepiestID = id

    sleepiestMin = -1
    sleepiestNum = -1
    for m, num in pattern[sleepiestID].items():
        if num > sleepiestNum:
            sleepiestNum = num
            sleepiestMin = m

    return(int(sleepiestID) * int(sleepiestMin))

def part2(input):
    pattern = {}
    input.sort(key=getDate)

    guardRE = re.compile("^.*\d\d\d\d-\d\d-\d\d \d\d:\d\d.*Guard #(\d+).*$")
    sleepRE = re.compile("^.*\d\d\d\d-\d\d-\d\d \d\d:(\d\d).*falls.*$")
    wakeRE = re.compile("^.*\d\d\d\d-\d\d-\d\d \d\d:(\d\d).*wake.*$")
    for entry in input:
        if guardRE.match(entry):
            guardID = guardRE.findall(entry)[0]
        elif sleepRE.match(entry):
            sleep = int(sleepRE.findall(entry)[0])
        elif wakeRE.match(entry):
            wake = int(wakeRE.findall(entry)[0])
            for m in range(sleep, wake):
                num = 1
                if guardID not in pattern:
                    pattern[guardID] = {}
                if m in pattern[guardID]:
                    num = pattern[guardID][m]
                    num += 1
                pattern[guardID][m] = num

    sleepiestID = -1
    sleepiestMinutes  = -1
    for id, sleep in pattern.items():
        totalSleep = 0
        for m, num in sleep.items():
            totalSleep += num
        if totalSleep > sleepiestMinutes:
            sleepiestMinutes = totalSleep
            sleepiestID = id

    sleepiestID = -1
    sleepiestMin = -1
    sleepiestNum = -1
    for id, sleep in pattern.items():
        for m, num in sleep.items():
            if num > sleepiestNum:
                sleepiestNum = num
                sleepiestMin = m
                sleepiestID = id

    return(int(sleepiestID) * int(sleepiestMin))


print("Part1: ", part1(input))
print("Part2: ", part2(input))
````

<a name="day5" /></a>
## Day 5: Alchemical Reduction - Racket
For [day 5](https://adventofcode.com/2018/day/5) I have chosen [Racket](https://racket-lang.org/).  I haven't written much in racket and what I have was quite a few years ago.  To start with I quite liked using the DrRacket environment and REPL, but after a while I was struggling to get my head into the mindset required for racket.  I managed to do a recursive solution, but it is pretty ugly and slow.

```` scheme
#lang racket/base
; Code for Day 5 of Advent of Code: https://adventofcode.com/2018/day/5
; Racket Solution
;
; Copyright (C) 2018 Lawrence Woodman <lwoodman@vlifesystems.com>
; Licensed under an MIT licence.  Please see LICENCE.md for details.

(require racket/file
         racket/list
         racket/math
         racket/string)

(define (opposite-case? a b)
  (and (not (equal? a b)) (equal? (string-upcase a) (string-upcase b))))

(define input
  (map string (string->list (string-trim (file->string "day5.input")))))


(define (react input start)
  (if (>= start (- (length input) 1))
      input
      (let ([trial (list-tail input start)])
        (let ([a (first trial)]
              [b (second trial)])
          (if (opposite-case? a b)
              (react (append (take input start)
                             (list-tail input (+ start 2)))
                     start)
              (react input (+ start 1)))))))


(define (chainreaction input)
  (let ([new-input (react input 0)])
    (if (= (length input) (length new-input))
        input
        (chainreaction new-input))))

(define (part1 input)
  (length (chainreaction input)))

(define (part2 input)
  (second
   (let* ([letters '("a" "b" "c" "d" "e" "f" "g" "h"
                            "i" "j" "k" "l" "m" "n" "o" "p"
                            "q" "r" "s" "t" "u" "v" "w" "x" "y" "z")]
          [lens (map (lambda (l)
                       (let ([new-input
                            (filter (lambda (e)
                                      (not (equal? (string-downcase l)
                                                   (string-downcase e))))
                                    input)])
                         (list l (part1 new-input))))
                     letters)])
          (for/fold ([shortest (list "!" (length input))])
                    ([l lens])
            (if (< (second l) (second shortest))
                l
                shortest)))))


(printf "Part1: ~a\n" (part1 input))
(printf "Part2: ~a\n" (part2 input))
````

<a name="day6" /></a>
## Day 6: Chronal Coordinates - Commodore 128 Basic

I have decided to go [retro](/articles/tag/retro/) for [day 6](https://adventofcode.com/2018/day/6) and have created the solution on the [Commodore 128](https://en.wikipedia.org/wiki/Commodore_128) using its built-in [Basic 7.0](https://en.wikipedia.org/wiki/Commodore_BASIC).  It must have been over 25 years since I have written anything in basic on the 128, so it was great fun to have an excuse to tinker with it again.   The only real problem is that it took days for it to do the calculation.

<img src="/img/articles/c128_aoc_day6.png" title="Screenshot of Basic code on c128" />


```` basic
10 rem code for day 6 of advent of code
20 rem commodore 128 basic
30 rem
40 rem copyright (c) 2018 lawrence woodman
50 rem licensed under an mit licence.

100 gosub 6000: rem get coordinate stats
110 dim di(nc)
120 gosub 8000: rem part1
130 gosub 9000: rem part2
140 end


1000 rem manhatten distance subroutine
1001 rem args: ma mb mc md
1002 rem representing: x1 y1 x2 y2
1003 rem ret: d
1010 d = abs(ma-mc) + abs(mb-md)
1020 return


2000 rem create distances subroutine
2001 rem args: x, y
2002 rem populates: di array (distances)
2010 for n = 1 to nc
2020 read ix, iy
2030 ma = x: mb = y: mc = ix: md = iy
2040 gosub 1000: rem manhatten distance
2050 di(n) = d
2060 next n
2070 restore 10020
2080 return


3000 rem check if clashes
3001 rem args: di
3002 rem ret: c (1 if clashes else 0)
3010 nd = (mx+2)*(my+2): rem nearest distance
3020 nn = 100: rem nearest num
3030 c = 0: rem clash = false
3040 for n = 1 to nc
3050 if di(n) = nd then c = 1: else begin
3060 if di(n) < nd then nn = n: nd = di(n): c = 0
3070 bend
3080 next n
3090 return

4000 rem make areas and excludes subroutine
4010 dim e(nc): rem excludes
4020 dim a(nc): rem areas
4030 print "progress:   0%";
4040 for y = 0 to my+2
4050 print chr$(20);chr$(20);chr$(20);chr$(20);
4060 print using "###%";int((y*100)/(my+2));
4070 for x = 0 to mx+2
4080 gosub 2000: rem create distances to coords from x,y
4090 gosub 3000: rem check if clashes
4100 if c = 1 then n=255: else n=nn
4110 if n <> 255 then begin
4120 if x = 0 then e(n) = 1
4130 if y = 0 then e(n) = 1
4140 if x = mx+2 then e(n) = 1
4150 if y = my+2 then e(n) = 1
4160 if e(n) = 0 then a(n) = a(n) + 1
4170 bend
4180 next x
4190 next y
4200 print
4210 return

5000 rem find biggest subroutine
5010 bg = 0
5020 for n = 1 to nc
5030 if a(n) > bg then begin
5040 if e(n) = 0 then bg = a(n)
5050 bend
5060 next n
5070 return

6000 rem get coordinate stats (nc, mx, my)
6010 mx = 0
6020 my = 0
6030 read nc: rem number of coordinates
6040 for n = 1 to nc
6050 read x, y
6060 if x > mx then mx = x
6070 if y > my then my = y
6080 next n
6090 restore 10020
6100 return

7000 rem find safe region size subroutine
7010 rs = 0: rem region size
7020 print "progress:   0%";
7030 for y = 0 to my
7040 print chr$(20);chr$(20);chr$(20);chr$(20);
7050 print using "###%";int((y*100)/my);
7060 for x = 0 to mx
7070 td = 0: rem total distance
7080 for n = 1 to nc
7090 read ix, iy
7100 ma = x: mb = y: mc = ix: md = iy
7110 gosub 1000: rem manhatten distance
7120 td = td + d
7130 next n
7140 if td < 10000 then rs = rs + 1
7150 restore 10020
7160 next x
7170 next y
7180 print
7190 return

8000 rem part1 subroutine
8010 print "part1": print "====="
8020 gosub 4000: rem make areas and excludes
8030 gosub 5000: rem find biggest
8040 print "answer: ";bg
8050 return

9000 rem part2 subroutine
9010 print "part2": print "====="
9020 gosub 7000: rem find safe region size subroutine
9030 print "answer: ";rs
9040 return


10000 rem day6 test input
10010 data 50: rem number of coordinates
10020 rem the coordinates
10030 data 154, 159, 172, 84, 235, 204, 181, 122
10040 data 161, 337, 305, 104, 128, 298, 176, 328
10050 data 146, 71, 210, 87, 341, 195, 50, 96
10060 data 225, 151, 86, 171, 239, 68, 79, 50
10070 data 191, 284, 200, 122, 282, 240, 224, 282
10080 data 327, 74, 158, 289, 331, 244, 154, 327
10090 data 317, 110, 272, 179, 173, 175, 187, 104
10100 data 44, 194, 202, 332, 249, 197, 244, 225
10110 data 52, 127, 299, 198, 123, 198, 349, 75
10120 data 233, 72, 284, 130, 119, 150, 172, 355
10130 data 147, 314, 58, 335, 341, 348, 236, 115
10140 data 185, 270, 173, 145, 46, 288, 214, 127
10150 data 158, 293, 237, 311
````

<a name="day7" /></a>
## Day 7: The Sum of Its Parts - Pascal
For [day 7](https://adventofcode.com/2018/day/7) I have chosen [Pascal](https://en.wikipedia.org/wiki/Pascal_(programming_language)).  I haven't written anything in pascal for over five years and prior to that I hadn't written anything in pascal for about 17 years.  So I was more than a little rusty.  The code isn't very elegant but I just wanted to come up with a solution in the language.  I used the [free pascal](https://www.freepascal.org/) compiler to compile the code.

```` pascal
// Code for Day 7 of Advent of Code: https://adventofcode.com/2018/day/7
// Pascal Solution
//
// Copyright (C) 2018 Lawrence Woodman <lwoodman@vlifesystems.com>
// Licensed under an MIT licence.  Please see LICENCE.md for details.

program day7;

uses
  Sysutils, Strutils;

type
  step_details = array [0..2] of char;
  all_step_details = array of step_details;
  char_array = array of char;
  worker_details = record
    letter: char;
    secs: integer;
  end;

function num_seconds(const l: char) : integer;
begin
  num_seconds := ord(l) - 65 + 1 + 60;
end;

function get_steps(const filename: string) : all_step_details;
var
  i: integer;
  s: string;
  a: string;
  b: string;
  f: TextFile;
begin
  i := 0;
  assign(f, filename);
  reset(f);
  while not eof(f) do
  begin
    setlength(get_steps, i+1);
    readln(f, s);
    a := ExtractWord(2, s, [' ']);
    b := ExtractWord(8, s, [' ']);
    get_steps[i,0] := a[1];
    get_steps[i,1] := b[1];
    inc(i);
  end;
  close(f);
end;

function remove_letter(steps: all_step_details; letter: char) : all_step_details;
var
  i: integer;
  details: step_details;
begin
  i := 0;
  setlength(remove_letter, 0);
  for details in steps do
  begin
    if details[0] <> letter then
    begin
      setlength(remove_letter, i+1);
      remove_letter[i] := details;
      inc(i);
    end;
  end;
end;

function cando_letters(steps: all_step_details) : char_array;
var
  i: integer;
  s: step_details;
  l: char;
  waiting_letters: set of char;
  starting_letters: set of char;
begin
  i := 0;
  starting_letters := [];
  waiting_letters := [];
  for s in steps do
  begin
    include(starting_letters, s[0]);
    include(waiting_letters, s[1]);
  end;
  for l in starting_letters do
  begin
    if not(l in waiting_letters) then
    begin
      setlength(cando_letters, i+1);
      cando_letters[i] := l;
      inc(i);
    end;
  end;
end;

// This should be included as part of cando_letters
function waiting_letters(steps: all_step_details) : char_array;
var
  i: integer;
  s: step_details;
  l: char;
  waiting_letters_set: set of char;
begin
  i := 0;
  waiting_letters_set := [];
  for s in steps do
  begin
    include(waiting_letters_set, s[1]);
  end;
  for l in waiting_letters_set do
  begin
    setlength(waiting_letters, i+1);
    waiting_letters[i] := l;
    inc(i);
  end;
end;

function part1 (steps: all_step_details) : string;
var
  l: char;
  next_letter: char;
  last_letter: char;
begin
  part1 := '';
  while length(steps) > 0 do
  begin
    last_letter := waiting_letters(steps)[0];
    next_letter := 'Z';
    for l in cando_letters(steps) do
    begin
      if l < next_letter then
      begin
        next_letter := l;
      end;
    end;
    steps := remove_letter(steps, next_letter);
    part1 := part1+next_letter;
  end;
  part1 := part1+last_letter;
end;

function isLetterBeingWorkedOn(workers: array of worker_details; l: char)
: boolean;
var
  w: worker_details;
begin
  isLetterBeingWorkedOn := false;
  for w in workers do
  begin
    if w.letter = l then
    begin
      isLetterBeingWorkedOn := true;
      break;
    end;
  end;
end;

function part2 (steps: all_step_details) : integer;
var
  i: integer;
  l: char;
  seconds: integer;
  workers: array of worker_details;
  last_letter: char;

begin
  setlength(workers, 5);
  for i := 0 to 5 do
  begin
    workers[i].letter := '.';
    workers[i].secs := -1;
  end;
  seconds := 0;
  while length(steps) > 0 do
  begin
    for i := 0 to 5 do
    begin
      if workers[i].letter <> '.' then
      begin
        workers[i].secs := workers[i].secs-1;
        if workers[i].secs = 0 then
        begin
          steps := remove_letter(steps, workers[i].letter);
          workers[i].letter := '.';
          workers[i].secs := -1;
        end;
      end;
    end;

    for i := 0 to 5 do
    begin
      if workers[i].letter = '.' then
      begin
        last_letter := waiting_letters(steps)[0];
        for l in cando_letters(steps) do
        begin
          if not(isLetterBeingWorkedOn(workers, l)) then
          begin
            workers[i].letter := l;
            workers[i].secs := num_seconds(l);
            break;
          end;
        end;
      end;
    end;
    inc(seconds);
  end;
  part2 := seconds + num_seconds(last_letter) -1;
end;

var
  steps: all_step_details;
begin
  steps := get_steps('day7.input');
  writeln('Part1: ', part1(steps));
  writeln('Part2: ', part2(steps));
end.
````

<a name="day8" /></a>
## Day 8: Memory Maneuver - F#
For [day 8](https://adventofcode.com/2018/day/8) I have chosen [F#](https://fsharp.org/).  I've written very little in F#, but when I have I've really liked the language.  It was great to play with it again and I found today's problem a good fit for a functional style solution.

```` fsharp
// Code for Day 8 of Advent of Code: https://adventofcode.com/2018/day/8
// F# Solution
//
// Copyright (C) 2018 Lawrence Woodman <lwoodman@vlifesystems.com>
// Licensed under an MIT licence.  Please see LICENCE.md for details.

open System.IO
open System

type Node = {Children: list<Node>; Meta: array<Int32>}

let rec GetChildren (header : array<Int32>) =
  let numChildNodes = header.[0]
  let folder acc i =
    let (totalNumNums,pos,children) = acc
    let (childNode, numNums) = ParseHeader header.[pos..]
    (totalNumNums+numNums, pos+numNums, childNode::children)
  let (totalNumNums,pos,children) =
    List.fold folder (0,2,[]) [0..numChildNodes-1]
  (List.rev children, totalNumNums)

and ParseHeader header =
  let numMetadata = header.[1]
  let (children, numNums) = GetChildren header
  let metadata =
    if numMetadata > 0 then
      header.[numNums+2..numNums+1+numMetadata]
    else
      [||]
  let totalNumNums = 2+numNums+numMetadata
  ({Children = children; Meta = metadata}, totalNumNums)

let rec AddMetadata (node) =
  let childrenMetadataSum =
    node.Children
    |> List.map AddMetadata
    |> List.sum
  childrenMetadataSum + Array.sum(node.Meta)

let rec AddMetadata2 (node) =
  let mapper m =
    if m <= List.length(node.Children) then
      AddMetadata2 node.Children.[m-1]
    else
      0
  if List.length(node.Children) = 0 then
    Array.sum(node.Meta)
  else
    node.Meta
    |> Array.map mapper
    |> Array.sum

let Part1 input =
  let (root, numNums) = ParseHeader input
  AddMetadata root

let Part2 input =
  let (root, numNums) = ParseHeader input
  AddMetadata2 root

let ReadInput filename : array<Int32> =
  let InputFile = File.ReadAllLines(filename)
  InputFile.[0].Split ' '
  |> Seq.map System.Int32.Parse
  |> Seq.toArray

let Input = ReadInput("day8.input")
printfn "Part1: %d" (Part1 Input)
printfn "Part2: %A" (Part2 Input)
````

<a name="day9" /></a>
## Day 9: Marble Mania - Julia
For [day 9](https://adventofcode.com/2018/day/9) I have chosen [Julia](https://julialang.org/).  I've never written anything in Julia so this was complete new for me.  I found it pretty easy to get a solution written and really like this clear and good looking language.  I particularly like the type system and its dispatch methods.

```` julia
# Code for Day 9 of Advent of Code: https://adventofcode.com/2018/day/9
# Julia Solution
#
# Copyright (C) 2018 Lawrence Woodman <lwoodman@vlifesystems.com>
# Licensed under an MIT licence.  Please see LICENCE.md for details.

using Printf

mutable struct Game
  numPlayers
  lastMarble
  current
  marbles
  Game(numPlayers, lastMarble) = new(numPlayers, lastMarble, -1, Dict())
  Game() = new(0, 0, -1, Dict())
end

mutable struct Marble
  prev
  next
end

function start(g::Game)
  g.marbles = Dict(0 => Marble(0,0))
  g.current = 0
end

function next(g::Game, value)
  g.marbles[value].next
end

function prev(g::Game, value)
  g.marbles[value].prev
end

function prev7(g::Game)
  v = g.current
  for i in 1:7
    v = prev(g, v)
  end
  v
end

function remove(g::Game, value)
  prev = g.marbles[value].prev
  next = g.marbles[value].next
  g.marbles[prev].next = next
  g.marbles[next].prev = prev
  g.current = next
  delete!(g.marbles, value)
end

function insert(g::Game, beforeValue, value)
  before = g.marbles[beforeValue]
  beforePrev = before.prev
  g.marbles[value] = Marble(before.prev, beforeValue)
  g.marbles[beforeValue].prev = value
  g.marbles[beforePrev].next = value
  g.current = value
end

function add(g::Game, value)
  if value % 23 == 0
    throw(ArgumentError("can't add multiple of 23"))
  end
  next1 = next(g, g.current)
  next2 = next(g, next1)
  insert(g, next2, value)
end

function getInput(filename)
  game = Game()
  open(filename) do file
    for ln in eachline(file)
      gameWords = split(ln)
      game = Game(parse(Int32, gameWords[1]), parse(Int32, gameWords[7]))
      break
    end
  end
  game
end

function play(g::Game)
  playerNum = 1
  start(g)
  scores = Dict()
  for m in 1:g.lastMarble
    if m % 23 == 0
      removeValue = prev7(g)
      remove(g, removeValue)
      playerScore = get(scores, playerNum, 0)
      scores[playerNum] = playerScore+m+removeValue
    else
      add(g, m)
    end
    playerNum = playerNum + 1
    if playerNum >= g.numPlayers
      playerNum = 0
    end
  end
  highestScore = 0
  for (p, s) in scores
    if s > highestScore
      highestScore = s
    end
  end
  highestScore
end


function part1(game)
  play(game)
end

function part2(game)
  game.lastMarble = game.lastMarble*100
  play(game)
end

game = getInput("day9.input")
@printf "part1: %d\n" part1(game)
@printf "part2: %d\n" part2(game)
````

<a name="day10" /></a>
## Day 10: The Stars Align - JavaScript

For [day 10](https://adventofcode.com/2018/day/10) I have chosen [JavaScript](https://en.wikipedia.org/wiki/JavaScript).  I've never really written anything in JavaScript, other than tinkering with existing code, despite having the Rhino book sitting on a bookshelf behind me.  It was relatively straight-forward to adapt my solution to it and I was impressed with its runtime speed.


```` html
<!--
  Code for Day 10 of Advent of Code: https://adventofcode.com/2018/day/10
  JavaScript Solution

  Copyright (C) 2018 Lawrence Woodman <lwoodman@vlifesystems.com>
  Licensed under an MIT licence.  Please see LICENCE.md for details.
-->
<html>
  <head>
  <title>Advent of Code 2018 - Day 10</title>
  <style>
    #part1-answer {font-family: monospace;}
  </style>
  </head>
  <body>
    <h1>Advent of Code 2018 - Day 10</h2>
    <strong>Enter input file</strong></br>
    <input type="file" id="file-input" />
    <h2>Part1</h2>
    <p id="part1-answer"></p>

    <h2>Part2</h2>
    <p id="part2-answer"></p>

    <script type='text/javascript'>

      function readInputFile(e) {
        var file = e.target.files[0];
        if (!file) {
          return;
        }
        var reader = new FileReader();
        reader.onload = function(e) {
          var input = e.target.result.trim().split("\n");
          points = getPoints(input);
          msg = findMessage(points);
          part1(msg);
          part2(msg);
        };
        reader.readAsText(file);
      }

      function findBoundaries(points) {
        minX = points[0].px
        minY = points[0].py
        maxX = points[0].px
        maxY = points[0].py
        for (i = 0; i < points.length; i++) {
          if(points[i].px < minX) {minX = points[i].px;}
          if(points[i].py < minY) {minY = points[i].py;}
          if(points[i].px > maxX) {maxX = points[i].px;}
          if(points[i].py > maxY) {maxY = points[i].py;}
        }
        return {minX: minX, minY: minY, maxX: maxX, maxY: maxY};
      }

      function makeMap(boundaries, points) {
        mapMaxX = 100
        mapMaxY = 100
        numVisiblePoints = 0
        minX = boundaries.minX
        minY = boundaries.minY
        width = boundaries.maxX-minX
        height = boundaries.maxY-minY
        if (width < mapMaxX) {mapMaxX = width;}
        if (height < mapMaxY) {mapMaxY = height;}
        plot = new Array(mapMaxY+1);
        for (y = 0; y <= mapMaxY; y++) {
          row = new Array(mapMaxX+1);
          for (x = 0; x <= mapMaxX; x++) {
            hasPoint = false
            for (i = 0; i < points.length; i++) {
              px = points[i].px; py = points[i].py;
              if (x == px-Math.abs(minX) && y == py-Math.abs(minY)) {
                hasPoint = true
                row[x] = '#';
                numVisiblePoints++;
              }
            }
            if (!hasPoint) {row[x] = '.';}
          }
          plot[y] = row;
        }
        return {plot: plot, size: (mapMaxX+1)*(mapMaxY+1),
                numVisiblePoints: numVisiblePoints};
      }

      function drawMap(map) {
        answerElement = document.getElementById('part1-answer');
        answerElement.innerHTML = "";
        for (y = 0; y < map.plot.length; y++) {
          row = map.plot[y];
          for (x = 0; x < row.length; x++) {
            answerElement.innerHTML += row[x];
          }
          answerElement.innerHTML += "<br />";
        }
      }

      function findMessage(points) {
        answerElement = document.getElementById('part1-answer');
        smallestMap = {plot: new Array(0), size: 0};
        secsMessage = 20000;
        for (sec = 0; sec < 20000; sec++) {
          if (sec % 1000 == 0) {
            answerElement.innerHTML = "Finding message: "+sec/200+"%";
            console.log("Finding message: "+sec/200+"%");
          }
          boundaries = findBoundaries(points);
          if (boundaries.maxX - boundaries.minX < 100 &&
              boundaries.maxY - boundaries.minY < 100) {
            map = makeMap(boundaries, points);
            if (map.numVisiblePoints = points.length) {
              if (smallestMap.size == 0 ||
                  map.size < smallestMap.size) {
                smallestMap = map;
                secsMessage = sec;
              }
            }
          }
          points = movePoints(points);
        }
        answerElement.innerHTML = "Finding message: 100%";
        console.log("Finding message: 100%");
        return {map: smallestMap, secs: secsMessage};
      }

      function movePoints(points) {
        newPoints = new Array(points.length);
        for (i = 0; i < points.length; i++) {
          px = points[i].px; py = points[i].py;
          vx = points[i].vx; vy = points[i].vy;
          newPoints[i] = {px: px+vx, py: py+vy, vx: vx, vy: vy};
        }
        return newPoints;
      }

      function getPoints(input) {
        points = new Array(input.length)
        for (i = 0; i < input.length; i++) {
          pointValues = input[i].match(
            /^position=<\s*(.*?),\s*(.*?)> velocity=<\s*(.*?),\s*(.*?)>$/
          );
          points[i] = {px: parseInt(pointValues[1], 10),
                       py: parseInt(pointValues[2], 10),
                       vx: parseInt(pointValues[3], 10),
                       vy: parseInt(pointValues[4], 10)};
        }
        return points;
      }

      function part1 (msg) {
        drawMap(msg.map);
      }

      function part2 (msg) {
        document.getElementById('part2-answer').innerHTML = msg.secs
      }

      document.getElementById('file-input')
        .addEventListener('change', readInputFile, false);
    </script>
  </body>
</html>
````

<a name="day11" /></a>
## Day 11: Chronal Charge - Pyret

For [day 11](https://adventofcode.com/2018/day/11) I have chosen [Pyret](https://www.pyret.org).  I heard about Pyret a while ago and this puzzle gave me the chance to give it a go.  It is designed to be a good introductory programming language with an emphasis on functional programming.  It turned out to be quite a nice language and I really liked the ability to add tests while defining a function using the `where` keyword.

```` fsharp
# Code for Day 11 of Advent of Code: https://adventofcode.com/2018/day/11
# Pyret Solution
#
# Copyright (C) 2018 Lawrence Woodman <lwoodman@vlifesystems.com>
# Licensed under an MIT licence.  Please see LICENCE.md for details.

fun calc-power-level(x, y, serial-number):
  rack-id = x + 10
  power-level = ((rack-id * y) + serial-number) * rack-id
  power-level-str = num-to-string(power-level)
  if string-length(power-level-str) >= 3:
    end-pos = string-length(power-level-str) - 3
    hundred = string-char-at(num-to-string(power-level), end-pos)
    string-to-number(hundred).or-else(0) - 5
  else:
    -5
  end
where:
  calc-power-level(122,79,57) is -5
  calc-power-level(217,196,39) is 0
  calc-power-level(101,153,71) is 4
end

fun make-grid(serial-number, size):
  fun build-row(y :: Number) -> Array<Number>:
    fun build-cell(x :: Number) -> Number:
      calc-power-level(x, y, serial-number)
    end
    build-array(build-cell, size + 1)
  end
  build-array(build-row, size + 1)
where:
  make-grid(57, 300).get-now(79).get-now(122) is -5
  make-grid(39, 300).get-now(196).get-now(217) is 0
  make-grid(71, 300).get-now(153).get-now(101) is 4
  make-grid(8, 5).get-now(5).get-now(3) is 4
  make-grid(8, 5).length() is 6
end

fun calc-square-power(grid, square-size, top-left-x, top-left-y):
  row-positions = range(top-left-y, top-left-y + square-size)
  col-positions = range(top-left-x, top-left-x + square-size)
  fun sum-row(y):
    fold(lam(sum, x): sum + grid.get-now(y).get-now(x) end, 0, col-positions)
  end
  fold(lam(sum, y): sum + sum-row(y) end, 0, row-positions)
where:
  calc-square-power(make-grid(18,300), 3, 33, 45) is 29
  calc-square-power(make-grid(42,300), 3, 21, 61) is 30
end

fun calc-next-square-power(
  grid, square-size, top-left-x, top-left-y, previous-power
):
  col-positions = range(top-left-x, top-left-x + square-size)
  row-positions = range(top-left-y, (top-left-y + square-size) - 1)
  col-sum = fold(
    lam(sum, x):
      y = (top-left-y + square-size) - 1
      sum + grid.get-now(y).get-now(x)
    end,
    0,
    col-positions)
  row-sum = fold(
    lam(sum, y):
      x = (top-left-x + square-size) - 1
      sum + grid.get-now(y).get-now(x)
    end,
    0,
    row-positions)
  (previous-power + col-sum) + row-sum
where:
  calc-next-square-power(make-grid(18,300), 3, 33, 45, 14) is 29
end

fun find-largest-square(grid, square-size):
  row-positions = range(1, 301 - square-size)
  col-positions = range(1, 301 - square-size)
  fun square-powers(acc, y):
    fold(
      lam(acc-inner, x):
        square-power = calc-square-power(grid, square-size, x, y)
        max-total-power = acc-inner.get(0)
        if square-power > max-total-power:
          [list: square-power, x, y]
        else:
          acc-inner
        end
      end,
      acc,
      col-positions)
  end
  best = fold(
    lam(acc, y): square-powers(acc, y) end,
    [list: -1,0,0],
    row-positions)
  [list: best.get(1), best.get(2)]
where:
  find-largest-square(make-grid(18,300), 3) is [list: 33,45]
  find-largest-square(make-grid(42,300), 3) is [list: 21,61]
end

fun find-cell-highest-power-square(grid, x, y):
  square-sizes = range(2, grid.length())
  square-power = calc-square-power(grid, 1, x, y)
  best = fold(
    lam(acc, size):
      previous-square-power = acc.get(0)
      if (x + size) <= grid.length():
        if  (y + size) <= grid.length():
          next-square-power = calc-next-square-power(
            grid, size, x, y, previous-square-power)
          max-total-power = acc.get(1)
          if next-square-power > max-total-power:
            [list: next-square-power, next-square-power, size]
          else:
            acc.set(0, next-square-power)
          end
        else:
          acc
        end
      else:
        acc
      end
    end,
    [list: square-power, square-power, 1],
    square-sizes)
    [list: best.get(1), best.get(2)]
where:
  grid = make-grid(18,4)
  find-cell-highest-power-square(grid, 0, 0) is [list: -4, 1]
  find-cell-highest-power-square(grid, 1, 2) is [list: 9, 3]
  find-cell-highest-power-square(grid, 2, 2) is [list: 8, 3]
end

fun find-any-largest-square(grid):
  row-positions = range(1, grid.length())
  col-positions = range(1, grid.length())
  best = fold(
    lam(acc-y, y):
      fold(
        lam(acc-x, x):
          best = find-cell-highest-power-square(grid, x, y)
          max-power = acc-x.get(0)
          cell-max-power = best.get(0)
          if cell-max-power > max-power:
            size = best.get(1)
            [list: cell-max-power, x, y, size]
          else:
            acc-x
          end
        end,
        acc-y,
        col-positions)
    end,
    [list: -1, 0, 0, 1],
    row-positions)
  [list: best.get(1), best.get(2), best.get(3)]
where:
  find-any-largest-square(make-grid(18,4)) is [list: 1,2,3]
end

fun part1(serial-number):
  grid = make-grid(serial-number,300)
  coord = find-largest-square(grid, 3)
  x = num-to-string(coord.get(0))
  y = num-to-string(coord.get(1))
  string-append(string-append(x, ","), y)
where:
  part1(18) is "33,45"
  part1(42) is "21,61"
end

fun part2(serial-number, grid-size):
  grid = make-grid(serial-number,grid-size)
  coord = find-any-largest-square(grid)
  x = num-to-string(coord.get(0))
  y = num-to-string(coord.get(1))
  size = num-to-string(coord.get(2))
  string-append(string-append(string-append(string-append(x, ","), y), ","),size)
where:
  part2(18,4) is "1,2,3"
end

GRID-SIZE = 300
SERIAL-NUMBER = 7857
print(string-append("part1: ", part1(SERIAL-NUMBER)))
print(string-append("part2: ", part2(SERIAL-NUMBER, GRID-SIZE)))
````

## New Year's Eve Conclusion
It's new years eve and I've decided to call time on this challenge.  It was fun to work through them and try some new languages or revisit old ones, but it got quite tiring finding the time to write two solutions for each language (Tcl plus another).  The main problem was the time to learn the little idiosyncrasies of languages I was unfamiliar with.  That said I loved being able to create a solution using the Commodore 128's Basic 7.0.  As far as new languages Julia turned out to be a quick and beautiful looking language, while Pyret had a nice simple functional style with tests easily integrated with the function definitions.

For anyone interested in doing the Advent of Code challenges, they stay open on the website so you might like to give them a go in preperation for next year's.
