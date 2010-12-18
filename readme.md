# Rakeplayer

Rakeplayer is perhaps the most portable music library software out there. It's
definitely the most minimalistic. The entire music library, which is all Ogg
Vorbis files, is maintained and played from the command line. It's actually
quite powerful in some ways.


## Top-Level Organization of the Library

Rakeplayer's organization is inspired by how iTunes internally manages its
music. The entire music library resides within one directory, which we will call
the "top directory." From the point of view of the top directory, all song files
should be organized in the following fashion:

    <artist>/<album>/<tracknumber> <title>.ogg

For example:

    Nelly Furtado/Whoa, Nelly!/05 I'm Like a Bird.ogg


## Vorbis Comments

One of the biggest advantages to maintaining an Ogg Vorbis library is that the
file format is actually simple to understand. The Vorbis codec provides a way to
add textual metadata to a song file, so that meaningful information about the
song travels along with the song itself.

Unlike MP3's metadata system, ID3, Vorbis comments are actually readable and
very simple to implement. Comments take the form of

    FIELD=VALUE

For example:

    ARTIST=Nelly Furtado
    ALBUM=Whoa, Nelly!
    TRACKNUMBER=05
    TITLE=I'm Like a Bird

The command to write comments is called vorbiscomment(1). Refer to its man page
for a more thorough discussion about writing comments. However, with Rakeplayer,
you never need to use the vorbicomment(1) utility directly. You can let a Rake
task do the work for you.

Which Rake task to issue depends on what you want to update and where you are in
the library. If you want to update the comments for just one album, change into
that album's directory and execute

    $ rake update_album

If you want to update all the songs by a particular artist, change into that
artist's directory and issue

    $ rake update_artist

If you wish to update the Vorbis comments for every song in your library, you
can from anywhere just type

    $ rake update

PS. Rakeplayer adopts the suggestions put forth by the Xiph.org Foundation
concerning what to name the fields for Vorbis comments. Refer to
<http://xiph.org/vorbis/doc/v-comment.html> for more information.


## Playing Music

How to play music depends on the program you use.

If you use ogg123, remember you can pass a directory of song files as well as
individual tracks. For instance:

    # Play all of her songs
    $ ogg123 "Alicia Keys"

    # Play all the songs from this album
    $ ogg123 "Alicia Keys/Songs in A Minor"

    # Play just tracks 2 and 3
    $ ogg123 "Alicia Keys/Songs in A Minor/0{2,3}*"

MPlayer, on the other hand, doesn't know how to deal with directories. You must
use wildcard expansion or otherwise enumerate the songs:

    $ mplayer 'Nelly Furtado/Whoa, Nelly!'/*

    # Or, for example:
    $ find "Nelly Furtado" | xargs mplayer


## Sharing Your Library

You can use the tar(1) utility to share your music with others.

To share your entire library, go up one directory from the library's top
directory and issue

    $ tar -cj -f mylibrary.tar.bz2 <my library dir>

You can also create archives of particular sections of your collection:

    # Archive just one artist
    $ tar -cj -f beyonce.tar.bz2 Beyonce/

    # Archive just one album
    $ cd Beyonce
    $ tar -cj -f sashafierce.tar.bz2 "I Am... Sasha Fierce/"


## Maintaining Compilation Albums with Many Artists

Rakeplayer treats the artist directory named "Various Artists" in a special
manner. "Various Artists" is reserved for compilation albums with many artists.
Song files should be structured like this:

    Various Artists/<album>/<tracknumber> <artist> - <title>.ogg

For example:

    Various Artists/Tomb Raider Soundtrack/12 Leftfield - Song of Life.ogg

There are no special commands associated with compilation albums. Just run 

    $ rake update_album

and friends just like you always have. Rakeplayer understands that it should
process compilation albums differently. For instance, the Vorbis comments of the
above song look like this:

    ARTIST=Leftfield
    ALBUM=Tomb Raider Soundtrack
    TRACKNUMBER=12
    TITLE=Song of Life


## Library Statistics

A simple overview about your library can be viewed by running

    $ rake stats

You'll get an output like the following:

    No. artists:     53
    No. albums:      71
    No. songs:       734
    Total size:      2838 MB
