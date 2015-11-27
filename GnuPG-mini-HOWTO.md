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
