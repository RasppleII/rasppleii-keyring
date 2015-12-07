GnuPG mini-HOWTO for Raspple II users
=====================================


Understanding how public key encryption works
---------------------------------------------

You don't actually have to understand the complex mathematics or have
discussions about Alice and Bob and Charlie to understand how public key
encryption software like GnuPG works.  It can be explained simply, and one
major reason that it is not used more often is that so few people take the
time to do that.

Hopfeully we will correct that here.


### Traditional cryptography and its limitation

With traditional encryption methods, you use one key.  You encrypt with the
key at one end, and someone else uses the same key at the other to decrypt.
This requires that you somehow have a means of distributing the key only to
people who should have it.  If anyone else is ever able to obtain the key,
they have access to all messages sent using that key no matter when they were
sent.  The only secure way to share such a key is in person, which doesn't
work well if such an exchange is difficult or impossible.


### The solution: Public key cryptography

With public key cryptography, this problem is solved by each person having two
mathematically related keys.  The first key is called *private*.  It's a
carefully guarded secret and should never be transmitted over the Internet or
shared with anyone else.  If someone else ever gets their hands on it, YOUR
key is still compromised.  That's bad.

The other key is called *public*.  You put that one on your website, upload it
to special key servers, put info about it on business cards, whatever you
like.  You want people to have this one, and the way these things work is that
nobody who has it should ever be able to figure out what the contents of your
private key are.

But the keys are mathematically connected.  Anything done with one half of the
key requires the other half to verify and undo.  That means if someone sends a
message encrypted with your public key, only you can decrypt it with your
private key.


### Digital signatures

So now you can send a message to anyone whose public key you have, and only
they can read it.  How do they know *you* sent it?  It turns out you can
generate a message hash (similar to a SHA sum) with your private key.  A
person with your public key can verify that only your private key could have
generated the hash.  Like a SHA sum, changing one byte of the message will
invalidate the hash, so anyone receiving some digital content along with a
digital signature can be sure that you sent it and it arrived intact and
unmodified.

When this feature is combined with encryption with someone else's public key,
it allows you to send them a message that only they can decrypt, and that only
you could have sent.  If they have your public key to verify this, then they
can reply in turn knowing that only you can read the reply and in turn you
will know they sent it.  You and your correspondent need never have actually
met and may indeed be half a world apart and still accomplish this.  All that
remains is for the two of you to be somehow confident that the other person is
who they claim to be.


### The web of trust and keysinging parties

That last problem can't be solved by software alone.  How do you know that a
key belongs to the person in question?  The best way we've got to assure that
is for someone to verify!  Obviously it's best if you can do that yourself,
but if you and they are half a world apart and don't know each other, that
won't help you much.

The best way is for people who either know you or have checked your ID to
vouch for you.  This is often done at keysigning parties.  A keysigning party
is just what it sounds like: A bunch of folks get together and they bring some
generally acceptable form of ID with them, along with their key information.
You do likewise.  Laptops and other tablets are common accessories for
keysigning parties because you can pull keys right off of Internet key servers
if they've been uploaded to such places.

So you attend a keysigning party or otherwise have a key belonging to a person
whose identity you know or have verified, now what?  Well, the key will have
one or more email addresses associated with it.  You sign the key with your
own, and then you email it to them at the preferred email address on the key.
This extra step of emailing the signature to them verifies that they actually
own the email address connected to the key.  You couldn't do that if you just
uploaded the signature to an Internet key server.

A key that is widely signed, or signed by people who have a reputation for
care in signing keys (for example the Debian project insists that all
developers have their keys signed by another developer who has verified their
identity in person), might almost or just as good in your mind as one you have
signed yourself.  Or if the key is known to belong to a public personage, that
may be sufficient.  Or even perhaps if you don't care what the person's name
is as long as they are the person associated with certain online accounts.

Ultimately how much *you* trust a key is up to you.  It's not an exaggeration
that political activists living in countries where such activism is dangerous
use this technology.  If you were in such a situation, being absolutely
certain of every aspect of a person's identity might be really important and a
matter of life and death.

On the other hand, consider the digital signatures on Linux kernel releases.
You can verify that they've been signed by the same person for the past couple
of decades.  That person has always given the name Linus Torvalds.  If it were
ever discovered that had been an alias from the start, would it make that any
less his identity?  Some argue it would.  Your author is not one of those
people.


Keys 101
--------

Before you can do anything with GnuPG, you need a key.  You'll notice we're
skipping installation.  On a Linux box, you'll likely use your Linux package
manager to install GnuPG.  On any other system, you'll want to go over to the
GnuPG website <http://gnupg.org/> and download the binary or source release
for your system and compile or install it as necessary.  This document isn't
big enough to cover them all, so we're going to assume you can handle it.  We
will be using the command-line GnuPG program, either gpg or gpg2 (it doesn't
matter which.)


### Creating a key

Before you can do much else, you'll need a key.  Let's create one:

```bash
gpg --gen-key
```

Again, you can use `gpg2 --gen-key` as the two programs are interchangeable.
We'll use the latest gpg 1.x for this guide but gpg2 works just as well.  They
work the same and are developed in parallel
Your author has gpg2 on a Mac, you can use whatever you have installed.  The
output you'll get is almost identical, it's just that gpg2 has more
dependencies and interconnects to other programs more easily.

Here's the output on a Mac:

```
gpg (GnuPG) 1.4.19; Copyright (C) 2015 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

gpg: directory `/Users/tjcarter/.gnupg' created
gpg: new configuration file `/Users/tjcarter/.gnupg/gpg.conf' created
gpg: WARNING: options in `/Users/tjcarter/.gnupg/gpg.conf' are not yet active during this run
gpg: keyring `/Users/tjcarter/.gnupg/secring.gpg' created
gpg: keyring `/Users/tjcarter/.gnupg/pubring.gpg' created
Please select what kind of key you want:
   (1) RSA and RSA (default)
   (2) DSA and Elgamal
   (3) DSA (sign only)
   (4) RSA (sign only)
Your selection?
```

The default is RSA for signing and RSA for encrypting.  At the time of
writing, that is your best bet for a secure key.  If you live in a country
where it is illegal to use encryption, this document will probably be declared
illegal if your author does not advise you to select a sign-only option.

Actually, on second thought, it will probably be declared illegal anyway.
Pick the RSA/RSA default and get busy overthrowing the oppressive tyrants
already.  :)


```
Your selection? 1
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (2048)
```

At the time of writing, 2048 is considered secure.  Something that may be
useful for you to know is that barring any exploit in the algorithm, each new
bit doubles the difficulty in cracking the key.  So if 1024 bits is the
minimum, 1025 is twice as strong.  And 1026 bits is four times as strong.  You
probably should be aware that 1024 bit RSA has been defeated before.  Current
computers probably can't defeat 2048 bit RSA, barring whatever the NSA has
under their hats.

Your author is admittedly a bit paranoid and doesn't like generating new keys
all the time, so 4096 it will be.  And when we're asked about expiring after
we enter that, we'll select the default of 0:

```
What keysize do you want? (2048) 4096
Requested keysize is 4096 bits
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0)
Key does not expire at all
Is this correct? (y/N)
```

In this example we have a 4096 bit key that does not expire.  Answer y to
proceed.  You will then faced with some prompts for identity information.
Note, the output of gpg differs from that of gpg2 here slightly.  I'll enter
the information and then explain:

```
Is this correct? (y/N) y

You need a user ID to identify your key; the software constructs the user ID
from the Real Name, Comment and Email Address in this form:
    "Heinrich Heine (Der Dichter) <heinrichh@duesseldorf.de>"

Real name:
```

The info about what goes into a user ID here is different in gpg2 in that the
only prompt you're given there is this string:

```
GnuPG needs to construct a user ID to identify your key.
```

It's still name, email address, and comment.  Use your name and primary email
address, but **YOU SHOULD NOT PUT ANYTHING IN THE COMMENT FIELD**.  If you
want to read why, check [this article][gpg-comment-harmful] for details.
You're best to just leave the comment field blank.

[gpg-comment-harmful]: https://www.debian-administration.org/users/dkg/weblog/97

Anyway, we'll continue by filling in the user ID fields:

```
Real name: T. Joseph Carter
Email address: tjcarter@blocksfree.com
Comment:
You selected this USER-ID:
    "T. Joseph Carter <tjcarter@blocksfree.com>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit?
```

This looks good.  Your author's name is correct.  The email address is the one
used for this example.  And there is no comment.  Enter an "o" for okay and
we're off to the next step.

Now at this point it will ask you about a passphrase to protect your key.

```
You need a Passphrase to protect your secret key.
```

See, quite predictable.  A passphrase is like a password, only it can be much
longer.  It can be multiple words, and in fact the longer it is the better it
protects your key.  It's actually better to use more words than a short but
really complex password because brute force attacks against longer passwords
take a lot longer.  The important thing to remember is:

**DO NOT LOSE THE PASSPHRASE FOR YOUR KEY**

If you lose it, you've lost your key.  You've lost your identity.  And that is
another reason to use a phrase of words you will remember instead of a short
string of gibberish including upper and lowercase, numbers, an astrological
sign, the blood of a virgin goat, etc.  This needs to be something you won't
forget!  But it also needs to be something else another person can't guess.

If you are using gpg, it will just prompt you in the terminal window.  If you
are using gpg2, it may give you a GUI window prompt or a curses program.  It's
called "pinentry", and its designed by be more user-friendly.  Later on, you
can configure what pinentry program to use for gpg2.  For the original gpg,
what you get is what you get.

Now sit back and watch your key be generated...


```
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
..............+++++
.+++++
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
.+++++
......+++++
gpg: /Users/tjcarter/.gnupg/trustdb.gpg: trustdb created
gpg: key FACFC8EF marked as ultimately trusted
public and secret key created and signed.

gpg: checking the trustdb
gpg: 3 marginal(s) needed, 1 complete(s) needed, PGP trust model
gpg: depth: 0  valid:   1  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 1u
pub   4096R/FACFC8EF 2015-12-05
      Key fingerprint = 0C0C FFE8 3302 CF70 A3F0  9A1F 2603 E66E FACF C8EF
uid                  T. Joseph Carter <tjcarter@blocksfree.com>
sub   4096R/7FFEFAFA 2015-12-05

```

...actually, expecially on Linux systems, don't sit back and watch.  The
computer will probably "run out of entropy".  That means you needed more bytes
of true random data than your computer has.  The way Linux gets random data is
by having you do stuff with the computer and paying attention to the real
times between keypresses, mouse movements, network accesses, etc.  So now's a
good time to go catch up on your email, browse websites, watch cat videos on
Facebook, whatever...

Oh, one last thing: Note the key ID (the pub line), you'll need it later.  In
this example, it's `FACFC8EF`.  Sub-keys may matter later, but the main ID
identifies it.  Also, you'll note that the key generated above does not match
the one you'll find on keyservers and the Raspple II archive for the indicated
email address.  That's intentional; this key was created for this guide and
will not be used anywhere else.


### Generating a revokation certificate

Now that you've created a key, you're going to want to generate a way to
revoke it.  You might be wondering why you'd ever want to do that, but the
fact is that if your key is ever compromised or lost, you're going to want to
be able to tell people to stop using it.  If you have access to your key, you
can generate a revokation certificate at that time with a good reason and
everything.  But what if the computer that has your secret key is stolen and
you no longer have it to use?

You do it like this:

```bash
gpg --gen-revoke FACFC8EF
```

The output you'll get:

```

sec  4096R/FACFC8EF 2015-12-05 T. Joseph Carter <tjcarter@blocksfree.com>

Create a revocation certificate for this key? (y/N)
```

There's a confirmation step here because generating a revokation makes it
possible to destroy the usefulness of your key.  That's exactly what we want
in this case, so go ahead and answer y.


```
Create a revocation certificate for this key? (y/N) y
Please select the reason for the revocation:
  0 = No reason specified
  1 = Key has been compromised
  2 = Key is superseded
  3 = Key is no longer used
  Q = Cancel
(Probably you want to select 1 here)
Your decision?
```

At this point you don't know why you'll be using this revokation, so whether
to select 1 or 3 is possibly unknowable.  You could generate multiple
revokations for different reasons, but we're going to simply specify no reason
here.


```
Your decision? 0
Enter an optional description; end it with an empty line:
>
```

A word of note: The description you supply here will be viewable to anyone
using gpg to import this revokation we're about to generate, but it will not
be viewable in the plain text output.  None is specified here.


```
Reason for revocation: No reason specified
(No description given)
Is this okay? (y/N)
```

Looks good, answer y to proceed.  You'll be asked for your passphrase (either
in the terminal or (for gpg2) using pinentry.  This is the rest of the output:


```
Is this okay? (y/N) y

You need a passphrase to unlock the secret key for
user: "T. Joseph Carter <tjcarter@blocksfree.com>"
4096-bit RSA key, ID FACFC8EF, created 2015-12-05

Enter passphrase:
ASCII armored output forced.
Revocation certificate created.

Please move it to a medium which you can hide away; if Mallory gets
access to this certificate he can use it to make your key unusable.
It is smart to print this certificate and store it away, just in case
your media become unreadable.  But have some caution:  The print system of
your machine might store the data and make it available to others!
-----BEGIN PGP PUBLIC KEY BLOCK-----
Version: GnuPG v1
Comment: A revocation certificate should follow

iQIfBCABAgAJBQJWZNAsAh0AAAoJECYD5m76z8jvRl8P/iuP3v2o4JmAhOQ2dKrF
dw/159yJbyGdcMydht53UxByCNWxGqiKG9MhhCLzrKRNTy1yz0pXeI1skOrF3sLk
FYwb8JNA/7qD+xjHViykrpyNDQJCWs4TQhyFh2bna9l5K5jARgepGsU1OlPi28RI
hD6NEvXVOP9fr4q23SD6a4q5PZjdbAN7lzy0L5QwJXW7akuCLV1yfBUGoO6GJnVq
9uPpbxj816DnvuTSq/wWcGnnq/cMZwlo0w0rOVDweTn4bpRULnPPiqEUO+rylqyj
B3FwWEqfN+RzZmsQmpbuz7wgkvCl8IYP9yeYGHZxxTlEAAXEb8Isf7+g0MlyiN3R
DZSlkTN/LEHPoqcwnDnFTIJ1xggng8GaX33VSk94BzYZFRjA/KcZ2IpYY+VvLUOg
bNfx9O+oE/pH5eUuVXo4pDx/OWIcyYohVE7qq+LFGAQwQXgy5+OdFu8OXCRxIhox
e2xfi6e77KD1PPT+V5aqn/ISZvvx9Yw9WJ+gTRdT0njhhYsZR4GNrACDvv9/DCaF
SXyDMQI7bA4BGgsC1Pf662G5VQbtz/5jUJm2wEyhe5DhRBFcCufP7tjLXvTlDR/5
D2JhFA0yCy2zW4Egn/XB5/PLymatulKi5hK21303+IMEej1fi97TPK8AXHIEn2+k
A5U2uaIqz/2ItOEep6y5bQ+Q
=zO5y
-----END PGP PUBLIC KEY BLOCK-----
```

The PGP public key block (with delimiter lines) is the part you actually need.
As long as you have a copy of that, you can revoke your key.  And because you
have a copy of it for key ID FACFC8EF, you could maliciously revoke the key
used in this guide!  Which is one reason why the example key is only used for
this guide and nowhere else.
