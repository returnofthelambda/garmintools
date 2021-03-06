# GARMINTOOLS

[![Build Status](https://travis-ci.com/phako/garmintools.svg?branch=master)](https://travis-ci.com/phako/garmintools)
[![Build Status](https://api.cirrus-ci.com/github/phako/garmintools.svg)](https://cirrus-ci.com/github/phako/garmintools) 

This software provides Linux users with the ability to communicate
with the Garmin Forerunner 205 and Forerunner 305 via the USB
interface.  While this is the only Garmin unit that I own, I did
implement all of the documented Garmin protocols
(https://web.archive.org/web/20170627041709/http://www.garmin.com/support/pdf/IOSDK.zip) as of Rev C (May 19,
2006) over the USB physical link.  This means that if you have a
Garmin with a USB connection to a PC, you ought to be able to use this
software to communicate with it.

You will need to make sure that the 'garmin_gps' kernel module is
not loaded or compiled into your kernel.  This module conflicts
with the garmintools software.

If you're looking for a complete solution to all of your Linux Garmin
GPS needs, this is not it.  I own a Garmin Forerunner 305 and wrote
this code specifically so I could download and save data from that
particular GPS unit to my Linux machine.  I tried gpsbabel
(http://www.gpsbabel.org), but found that it did not have command line
options specific to the Forerunner run and lap data - all I could do
was get the tracklog.  That's how all of this got started.  I also
wanted a few other things, like the ability to convert a track log
into a Google maps encoded polyline, and (eventually) the ability to
generate PNG images of heart rate and elevation data.

You need to have the libusb library (http://libusb.sf.net) installed.
You must also have read and write permissions to the device file
through which the Garmin unit is accessed.  The easiest thing to do is
run the programs as root.  Alternatively, you can write a hotplug
script.  I haven't tried this yet.

As of version 0.01, this software gives you the ability to do the
following:

1) Get basic information from the Garmin unit, such as its software
   version and supported protocols.  To do this, use
   'garmin_get_info'.

2) Save runs and associated track logs from the Garmin unit to disk.
   To do this, use 'garmin_save_runs'.  Files are saved with the
   extension ".gmn" and are named by the run start time
   (e.g. '20070121T151814.gmn').  The .gmn file format is a binary
   file format that can be unpacked with the same functions that
   unpack the raw data from the Garmin unit itself.  It is
   substantially more efficient than the bloated XML format that
   Garmin uses.

   IMPORTANT NOTE: The output of garmin_save_runs will tell you where
   on your filesystem the actual .gmn files were saved.  The default
   is to save them in a directory tree (by year and month) under the
   current directory.  You can override this by setting the
   environment variable GARMIN_SAVE_RUNS to whatever directory you
   like.

   ANOTHER IMPORTANT NOTE: Old workouts are not deleted from the
   watch, and their laps hang around for a long time.  This once led
   me to clobber half a dozen saved runs with truncated files
   containing only the lap data (because the associated track logs had
   long since been overwritten in the Forerunner's memory).
   Therefore, if a file with the same name already exists, it is *not*
   overwritten.

   FINAL NOTE: As a result of that rather unpleasant experience, I
   wrote a Perl script 'fore2gmn.pl' (included in the 'extras'
   directory) that can convert a .hst file (the XML format exported
   from Garmin Training Center) to a set of .gmn files.  These .gmn
   files are saved in the current working directory without any of the
   year/month hierarchy business.  Using this script, I was able to
   restore the six files that I had lost.

3) Dump the contents of a .gmn file.  To do this, use 'garmin_dump'.
   The output of garmin_dump is XML-like, and is mainly meant to be
   used for debugging.

4) Print, in an XML-like format, the encoded polyline representation
   of a .gmn file (for Google maps) along with other information such
   as the start and center latitude/longitude, and the lat/lon
   bounding box.  To do this, use 'garmin_gmap' on a .gmn file.

In addition, the garmintools API in src/garmin.h gives you the ability
to read a .gmn file and do pretty much anything you want to it.

I chose to write this software in C.  C++ programmers (and I am one of
them) might have a look at the code and ask, "Why not do this in C++
and spare yourself all of the switch statements?"  I don't have a good
answer, just a bunch of half-answers, and I may end up changing my
mind.  The bottom line is that even after years of C++ programming, I
still like to stick with C when I can.  If you've ever had to deal
with aCC on HP-UX, you understand.

CAVEAT: I have only tested this software with the Forerunner 305 that
I own.  Users have reported that it also works with the Forerunner
205.  I don't know if it works with the Edge 205/305, the Forerunner
405, or any of the more recent Edge models.  

ONE MORE CAVEAT: The 'garmin_gmap' utility expects a .gmn file with a
D304 track point data type.  This is the data type used by the
Forerunner 305.  I WILL FIX THIS SOON.  Also, the output of
garmin_gmap is not for the faint of heart.  You will need to have your
own website where you can put this encoded polyline in an HTML file,
following the instructions on the Google maps API reference pages:

http://www.google.com/apis/maps/documentation/

I may make this easier some day.  For now, this is how it is.

Dave Bailey
Truckee, CA
22 February 2007

