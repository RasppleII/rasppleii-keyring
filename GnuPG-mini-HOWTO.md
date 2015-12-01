# GnuPG mini-HOWTO for Raspple II users


## What is public key cryptography

With traditional encryption methods, you use one key.  You encrypt with the
key at one end, and someone else uses the same key at the other to decrypt.
This requires that you somehow have a means of distributing the key only to
people who should have it.  If anyone else is ever able to obtain the key,
they have access to all messages sent using that key no matter when they were
sent.  The only secure way to share such a key is in person, which doesn't
work well if the two people are half a world apart.

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

You could theoretically encrypt a message with your private key.  The result
would be that it could only be decrypted with your public key.  But that
doesn't make a whole lot of sense because absolutely everybody has access to
your public key!

What does make sense is if you could use your private key to create a secure
magic number for your message to prove that you and only you could've written
it.  These magic numbers exist--they're made with something called message
digest or hash algorithms.  SHA256 is one such hash you might have heard of
before.  Well, your private key can generate something similar to that, only
in addition to hashing the message, it also hashes your identity with your
private key in a way that only your public key can verify.  We call that a
digital signature.

You get two-way secure communication by combining these two concepts.  A
sender will use the recipient's public key to encrypt a message, and their own
private key to sign it.  When the recipient gets the message, they check the
signature (requires the sender's public key) to verify who sent it, and their
own private key to decrypt the message.


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

The Raspple II archive (as well as Raspbian and Debian archives) have their
index files signed this way, and `apt` will verify the signature on the index.
That prevents tampering with the index.  And the index contains secure hashes
for the rest of the files.  In addition, any time a developer uploads a
package to the Raspple II archive, it includes an index of what the developer
uploaded which is signed by a developer.  This model, developed by the Debian
project, allows for a large group of people from around the world to work
together despite being located in different cities and even countries, yet
maintain integrity of the entire project.


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

From there, anyone who trusts your judgment (and your key) may trust the key
of anyone whose key you have signed.  This generates a web of trust as more
and more people sign each other's keys.


### Signing parties

When a bunch of people get together with their public keys, they can have a
signing party.  If you attend one, be prepared to provide information about
your public key and some form of identification to prove that you are who you
claim to be.  Typically, the person will sign your key and send their
signature to your email address.  That way they have proven not only your
identity, but that your email address is really your own.

You do the same for them.  Then when everybody checks their email, they can
import the new signatures into their keys and publish changes.  This is a
great way to build the web of trust quickly.  Apple // users could build a
pretty good web of trust quickly by having a signing party at KFest or the
other regional events.  It might only be of real interest to Raspple II
developers at the moment, but it's always good to use public key encryption
and you never know when it will start being useful to have it.

The Debian project, ultimately the origin of the operating system Raspple II
uses, requires that every one of its prospective new developers have a key
that has been signed by an existing developer.  Debian developers have key
signing parties any time a group of them gets together.
