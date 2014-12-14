#firebase-python
######A nice, easy Python Firebase integration

Supports streaming API but not authentication.

Requires `requests` and `sseclient`, which are on pip. If you don't know what that is, don't worry; just run `./setup.sh`.

Warning: The free level of Firebase only allows up to 50 concurrent connections. Don't come close to this or you'll start facing some really weird bugs!



##get and put

`get` gets the value of a Firebase at some URL, and `put` sets the value of a Firebase at URL to some data.

```python
>>> import firebase
>>> URL = 'lucid-lychee'  # see note on URLs at the bottom of documentation
>>> firebase.get(URL) == None  # this is an empty Firebase
True

>>> firebase.put(URL, 'tell me everything')  # can take a string
>>> firebase.get(URL)
u'tell me everything'

>>> firebase.put(URL, {'lucidity': 9001})  # or a dictionary
>>> firebase.get(URL)
{u'lucidity': 9001}

>>> firebase.put(URL, {'color': 'red'})  # replaces old value
>>> firebase.get(URL)
{u'color': u'red'}

>>> firebase.get(URL + '/color')
u'red'
```



##patch

`patch` adds new key value pairs to an existing Firebase, without deleting the old key value pairs.

```python
>>> import firebase
>>> URL = 'tibetan-tumbleweed'
>>> firebase.get(URL) == None
True

>>> firebase.patch(URL, {'taste': 'tibetan'})
>>> firebase.get(URL)
{u'taste': u'tibetan'}

>>> firebase.patch(URL, {'size': 'tumbly})  # patching does not overwrite
>>> firebase.get(URL)
{u'taste': u'tibetan', u'size': u'tumbly'}
```



##subscriber

`subscriber` takes a URL and callback function and calls the callback on every update of the Firebase at URL.

```python
>>> import firebase
>>> from pprint import pprint  # function which pretty prints objects
>>> URL = 'clumsy-clementine'
>>> S = firebase.subscriber(URL, pprint)  # pprint will be called on all Firebase updates
>>> S.start()  # will get called with initial value of URL, which is empty
{u'data': None, u'path': u'/'}

>>> firebase.put(URL, ';-)')  # will make S print something
{u'data': u';-)', u'path': u'/'}

>>> firebase.put(URL, {'status': 'mortified'})  # continuing from above
{u'data': {u'status': u'mortified'}, u'path': u'/'}
>>> firebase.patch(URL, {'reason': 'blushing'})  # this one is scary
{u'data': {u'reason': u'blushing'}, u'path': u'/'}
>>> firebase.get(URL)  # notice the update only gave us the new k,v without telling us
{u'status': u'mortified', u'reason': u'blushing'}

>>> firebase.put(URL, {'color': 'orange-red'})  # expect change from S
{u'data': {u'color': u'orange-red'}, u'path': u'/'}
>>> firebase.get(URL)  # put overwrites, but S doesn't tell us!
{u'color': u'orange-red'}

>>> firebase.put(URL + '/color', 'red')
{u'data': u'red', u'path': u'/color'}
```



##URLs
All URLs are internally converted to the apparent Firebase URL. This is done by the `firebaseURL` method.

```python
>>> import firebase

>>> firebase.firebaseURL('bony-badger')
'https://bony-badger.firebaseio.com/.json'

>>> firebase.firebaseURL('bony-badger/bones/humerus')
'https://bony-badger.firebaseio.com/bones/humerus.json'

>>> firebase.firebaseURL('bony-badger.firebaseio.com/')
'https://bony-badger.firebaseio.com/.json'
```
