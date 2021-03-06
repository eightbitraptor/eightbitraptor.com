::title::       Running MPD on OSX
::published::   2010-07-21
::tags::        sysadmin,music

After finally getting fed up and giving iTunes the boot, I got round to making MPD work on my Mac. and unfortunately, apt-get install it ain't!

First step is to actually get hold of and install [mpd](http://mpd.wikia.com/wiki/Music_Player_Daemon_Wiki), if you're using sensible and using [homebrew](http://mxcl.github.com/homebrew/) that's as easy as:

<pre>
brew install mpd
</pre>

Which will pull in all of the required dependancies and compile them all for you. Then comes the mpd config file. This is all pretty standard stuff, you can adapt from the standard and massively verbose example included with the mpd sources. Mine lives at `/usr/local/Cellar/mpd/0.15.9/share/doc/mpd/mpdconf.example`. The stuff you need to care about is:

<pre>
music_directory
playlist_directory
log_file
db_file
pid_file

mixer_type "software"
</pre>

Make sure these paths are all writeable by the user that you intend to run mpd as. In my case, I run mpd as the mpd user, and I made the mpd user and my normal user account members of group mpd.

What this amounts to is a music and playlist directory that the mpd user can read from and that I can add songs to. If you run a multi user system it's probably a good idea to put this somewhere outside of your home dir.

A special point regarding the mixer_type line: I have found this necessary when running on Snow Leopard to avoid mplayer crashing hard when trying to skip playing tracks, but as is normal with these things YMMV.

Once this has been set up you should be able to start mpd with 

<pre>
mpd --create-db
</pre>

and watch it chug away for a while depending on how much music you have.

<h3>Client</h3>

I use the excellent Theremin, which is an OSX native MPD client and does the job admirably. If that's not your style there are an excellent array of decent mpd clients out there.

<h3>Last.fm</h3>

[Last fm](http://last.fm) Scrobbling is acheived by the use of the [lastfmsubmitd daemon](http://www.red-bean.com/decklin/lastfmsubmitd/), and it's built in client lastmp. It's dead easy to set up. Clone the sources from Github and follow the instructions in the INSTALL file. The client scrobbler lives inside the contrib folder of the checkout.

I installed lastfmsubmitd to `/usr/local/bin` and created it's config file, and then simply copied the contrib/lastmp script to `/usr/local/bin`.

One gotcha if you're not familiar with running Python stuff (I'm not) is that lastmp will bail out complaining it can't import libmpdclient2. this is easily fixed with:

<pre>
easy_install py-libmpdclient2
</pre>

which will ramble on about installing eggs, I guess these are pythons equivalent of gems.

Both of these daemons apparently need to be running to actually make scrobbling happen so I normally wrap these up in @/usr/local/bin/music_starter@, which looks like

<pre>
  #! /bin/sh
  /usr/local/bin/mpd && \
  /usr/local/bin/lastfmsubmitd && \
  /usr/local/bin/lastmp
</pre>

<h3>Tying it all together</h3>

You can start the whole kit and caboodle on boot by creating the following plist file and adding it to launchctl:

<pre>
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple Computer//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>Disabled</key>
	<false/>
	<key>Label</key>
	<string>com.eightbitraptor.mpd</string>
	<key>Program</key>
  <string>/usr/local/bin/music_starter</string>
</dict>
</plist>
</pre>

I put this in `/Library/LaunchDaemons/com.eightbitraptor.mpd.plist`. Add it to launchd and start it like this:

<pre>
sudo launchctl load -w /Library/LaunchDaemons/com.eightbitraptor.mpd.plist
sudo launchctl start com.eightbitraptor.mpd.plist
</pre>

And Job done! Now you too can get rid of stinking iTunes. Now all that's left is to find something comparable to "mp3tagedit":
