## Quick Guide:

#### How Python native data structures map to Bencode elements:
* int -> Bint
* bytes -> Bbytes
* string -> Bbytes
* list and other iterables -> Blist, with all items in it cascaded to corresponding Bencode elements.
* dict -> Bdict , with all keys convert to strings and all values cascaded to corresponding Bencode elements.
* types not mentioned -> string -> Bbytes

#### BitTorrent file parse:

Generate magnet link from BitTorrent file

```
>>> from pretty_bencode3 import BitTorrent
>>> print(BitTorrent(r"I:\Download\torrent\kali-linux-2017.2-amd64.torrent").magnet)
magnet:?xt=urn:btih:16A7A586944F0C5C5797BD72E812BC23613AD9CE

```

#### Parse Bencode stream:

```
>>> from pretty_bencode3 import Bint, Bbytes, Blist, Bdict
>>>  print(repr(Bint.load(b"i123e")[0]))
Bint(123)
>>>  print(repr(Bbytes.load(b"3:123")[0]))
Bbytes(b'123')
>>> print(repr(Blist.load(b"li123e3:123e")[0]))
Blist([Bint(123), Bbytes(b'123')])
>>> print(repr(Bdict.load(b"d3:123i123ee")[0]))
Bdict({'123': Bint(123)})

```

#### Interact with native Python data structures:

```
>>> from pretty_bencode3 import Bint
>>>  a = Bint(123)
>>> a + a
Bint(246)
>>> a + 1
Bint(124)
>>> 1 - a
-122

```

Notice that Bbytes support bitwise and, or and xor operators:

```
>>> from pretty_bencode3 import Bbytes
>>> b = Bbytes(b"abc")
>>> b + b
Bbytes(b'abcabc')
>>> b.upper()
Bbytes(b'ABC')
>>> b ^ b"ABC" # __xor__() Overrided
Bbytes(b'   ')
>>> b + b"123"
Bbytes(b'abc123')
>>> b"123" + b
b'123abc'

```

```
>>> from pretty_bencode3 import Blist
>>> c = Blist([123, "123", b"123"]) 
>>> c
Blist([Bint(123), Bbytes(b'123'), Bbytes(b'123')]) # Notice all items in the list will be auto-cascaded.
>>> c[0]
Bint(123)
>>> c + [123]
Blist([Bint(123), Bbytes(b'123'), Bbytes(b'123'), Bint(123)])
>>> [123] + c
[123, Bint(123), Bbytes(b'abc'), Bbytes(b'abc')]
```

```
>>> from pretty_bencode3 import Bdict
>>> d = Bdict(key1=123, key2=456)
>>> d
Bdict({'key1': Bint(123), 'key2': Bint(456)}) # Notice all items in the list will be auto-cascaded.
>>> d["key1"]
Bint(123)
>>> Bdict.from_origin({123:123, 456:456})
Bdict({'123': Bint(123), '456': Bint(456)})

```