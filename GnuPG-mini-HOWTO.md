# GnuPG mini-HOWTO for Raspple II users


## Understanding how public key encryption works

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

