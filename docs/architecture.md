---
title: App architecture | Documentation
layout: documentation
permalink: 'docs/architecture/'
---

<div class="breadcrumb">
<a href="/docs">Documentation</a> &raquo;
App architecture
</div>

# App architecture
{% include toc.md %}

Let's go over the basics of how Turtl works.

## Data types

Your profile in Turtl is made of two main things: boards and notes.

### Notes

A note is fairly simple: it's a title, some text, a link, and/or a set of tags.
A note can have a file attached to it, whether that's a photo or a document. A
note can be in zero or more boards. A note that is not in any boards will only
show up under "All notes."

### Boards

A board is
a container of notes. It is a way to segment one set of notes from another, and
make your notes easier to manage. A board can contain not only notes, but also
nested boards under it. This means that you could have a board layout like:

~~~
My company
  Shared docs
  Passwords
  Accounting
~~~

If you open "My company" you will be able to view and search all notes under
that board, but also all the notes in any nested boards under it. Opening any
of the nested boards under "My company" will only show the notes directly in
that board.

### Other types

Your profile has a few other things in it that you don't have to think about,
mainly your keychain. Anything in your profile that is encrypted (a note, a
board, etc) has an entry on your keychain which allows that item to be
decrypted properly. Your keychain is encrypted with your master key (generated
from your email/password combo). [Learn more about Turtl's crypto](/docs/security).

## Sharing

Sharing allows you to collaborate on a board with someone else. The general
idea is this: each note has a unique key that decrypts it. This key is
encrypted with the key of any board the note is in, and this encrypted key is
stored in the note's data. So if you have an encrypted note, and you have the
key of a board that note is in, you can decrypt the note. Giving each object
in Turtl a unique key allows sharing only specific data with any set of people
without compromising the master key of your account.

Sharing is done only on a board basis. You cannot share single notes. You can,
however, put a single note into a board and share that board.

## Syncing

Whenever you change data in your profile, the changed objects are encrypted,
saved to local storage (IndexedDB), and the syncing system is notified of the
change. All changed objects are saved in an outgoing syncing table, which the
sync system periodically reads. If it finds changes, it sends them up to the
Turtl API server, in order, using a bulk send (all items are sent at once).

At the same time, the sync system long-polls the API for incoming changes to
your profile. This can happen when you use Turtl on another device, or if a
shared board you're a member of has changes on it. Then the reverse happens:
the changes are pulled down, saved (encrypted) to the local storage, and the
app is notified of the changes (at which point it loads the changes into
memory, decrypted, if it needs to).

If at any point Turtl gets disconnected, it holds outgoing sync data
indefinitely until it gets a connection. What this means is that after you log
in (which requires a connection), you can operate completely in offline mode
until you are connected again at which point all the changes you've made will
be synced.

Interested in how this all works in detail? [Read more about Turtl's syncing
system &raquo;](/docs/syncing)

