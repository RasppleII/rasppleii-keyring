# GnuPG mini-HOWTO for Raspple II users


## What is public key cryptography

One of the problems with public key cryptography is that a lot of people
don't use it when they should.  Or, they don't use it properly.  Both of
these problems stem from not understanding it.  That's no wonder since most
attempts to explain it seem to start with definitions of "symmetric" and
"asymmetric" ciphers, algorithms, and discussions of Alice and Bob and
Charlie.  Let's skip the jargon and the examples for now and focus on what
we want to do.


### Traditional encryption

If you want to communicate securely, you might want to use some kind of
encryption--that is to say, using secret codes.  The problem with the usual
way of doing this is that you and the person you're communicating with have
to have agreed upon your code in advance.

Worse, let's say who you're communicating with is your bank, rather than an
individual person.  Either they need to have a secret code for each one of
their customers, or one code for every customer.  The former would be a
total nightmare and the latter wouldn't be very secret!


### A better way

Public key cryptography uses very cool processes so that instead of having
one key that is used to encrypt and decrypt a coded message, you get two
paired keys.  Either key can encrypt, but they both work one-way.  So any
message ecnrypted with key "A" requires key "B" to decrypt.  Likewise if you
encrypt with key "B", you have to have key "A" to decrypt.  This works
because ... mathematics.  Explaining it fully would take at least one whole
book.

The way these key pairs get used is that you create one pair of keys and the
person you want to talk to creates their own pair.  But instead of calling
them "A" and "B", we're going to name them "private" and "public".  And then
each of you exchange *only* your "public" keys.  In fact, you not only share
your public keys with each other--you share them with the world!  That way
anyone can encrypt a message to you and be sure that *only* you can read it.

Your private message can encrypt messages that can be read using your public
key.  You'd think that encrypting stuff that way would be pointless since
*anyone* could read it, and you'd be half right.  There's no point in hiding
the message since anyone can read it, but if your key was used to make it,
anyone can tell that only *you* could have sent it.  That's very useful, and
we call that process "signing" the message.


### Putting it together

If you know someone's public key, you can send them a message that only they
can read, and which is signed by you.  If they have your public key, they
can verify that you wrote the message and send a reply that only you can
read.  And of course, you can verify that the reply came from them because
you already have their key.

Of course this only works as long as you keep your private key safe and
secret.  If anyone else gets their hands on it, well, it's bad.  If you lose
it, well, that's not good either.  We'll talk about both of those things in
concrete terms later.  For now just know that lost/compromised private keys
are a problem.

If you've followed everything so far, you can use public key cryptography.
You still need to learn the some commands, but you already understand the
basics.


## Signatures, trust, and paranoia

Okay, so you download a program off the internet.  How do you know your
program was downloaded intact?  How do you know it wasn't truncated in
transit, or corrupted?

In the old days of protocols like X/Y/Zmodem, the protocol would verify CRC32
values for each block.  The modern equivalent is a file containing magic
numbers called a *message hash* for each file.  We used to use MD5 or SHA1 for
this, but they're no longer regarded as secure enough.  At this writing,
SHA256 is a better choice.

Anyway, so you download the program and a .sha256sums file that goes with it.
And you run a tool that gives you the sha256sum for the program you
downloaded.  It matches.  But how do you know that someone didn't hack the
server, infect the program with a virus, and then modify the sha256sums file
to match the version with a virus?

It sounds paranoid, but that's happened before.  Multiple times, in fact!


### The digital signature

It turns out that the algorithms that we use in public key encryption can also
generate a "magic number" hash of a message or file and a person's private key
in such a way that a public key can verify the hash.  It's like encrypting a
message to the world, except the contents are left unencrypted.  If even one
byte of the message is modified, the signature won't match.

You will typically see GnuPG signatures in files ending with `.asc` or `.gpg`,
and you need to have the person's public key in order to verify these files.
We'll talk about how you can get a person's public key later on.


### The web of trust

There's one other problem.  Just as soon as you learn about the `--gen-key`
command in a minute, you can create a key that claims to belong to anybody you
want.  You could claim to be president@whitehouse.gov if you wanted to.  So
how do you know whose key belongs to whom?

There's a few ways to do this, and they run a wide gamut in terms of trust.
First of all, you can email a person at the email address on their key and ask
them to verify their public key.  If they do, you still don't know for certain
that they are who they claim to be, but at least you can be reasonably sure
that the email address is owned by the same person as the key.  That might be
good enough for your needs if the person's an established part of a community,
even if you don't personally know them.

Of course if you do personally know them, at least enough that you can say
they are who they claim to be, you can certify their key with your own by
generating a key signature.  This doesn't imply any trust of the person, just
that the key they were using when you signed it belongs to them.  Typically
they will sign yours in kind.

