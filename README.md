# student_ranepa
import hashlib

class MD5HashDict:
    def __init__(self, *args, **kwargs):
        self._data = {}  # {md5_hash: (original_key, value)}
        if args or kwargs:
            self.update(*args, **kwargs)
    def __setitem__(self, key, value):
        hashed_key = self._hash_key(key)
        self._data[hashed_key] = (key, value)

    def __getitem__(self, key):
        hashed_key = self._hash_key(key)
        return self._data[hashed_key][1]

    def __delitem__(self, key):
        hashed_key = self._hash_key(key)
        del self._data[hashed_key]

    def __contains__(self, key):
        hashed_key = self._hash_key(key)
        return hashed_key in self._data

    def __len__(self):
        return len(self._data)

    def __repr__(self):
        return repr({k: v for k, v in self.items()})

    def _hash_key(self, key):
        key_str = str(key).encode('utf-8')
        return hashlib.md5(key_str).hexdigest()

    def keys(self):
        return [original_key for original_key, _ in self._data.values()]

    def values(self):
        return [value for _, value in self._data.values()]

    def items(self):
        return [(original_key, value) for original_key, value in self._data.values()]

    def get(self, key, default=None):
        hashed_key = self._hash_key(key)
        if hashed_key in self._data:
            return self._data[hashed_key][1]
        return default

    def setdefault(self, key, default=None):
        hashed_key = self._hash_key(key)
        if hashed_key not in self._data:
            self._data[hashed_key] = (key, default)
        return self._data[hashed_key][1]

    def update(self, other=None, **kwargs):
        if other is not None:
            if hasattr(other, 'items'):
                for key, value in other.items():
                    self[key] = value
            else:
                for key, value in other:
                    self[key] = value
        for key, value in kwargs.items():
            self[key] = value

    def pop(self, key, default=None):
        hashed_key = self._hash_key(key)
        if hashed_key in self._data:
            value = self._data.pop(hashed_key)[1]
            return value
        if default is not None:
            return default
        raise KeyError(key)

    def popitem(self):
        if not self._data:
            raise KeyError("popitem(): dictionary is empty")
        hashed_key = next(iter(self._data))
        original_key, value = self._data.pop(hashed_key)
        return (original_key, value)

    def clear(self):
        self._data.clear()

    def copy(self):
        new_dict = MD5HashDict()
        new_dict._data = self._data.copy()
        return new_dict

    @classmethod
    def fromkeys(cls, iterable, value=None):
        new_dict = cls()
        for key in iterable:
            new_dict[key] = value
        return new_dict
      
