# student_ranepa
import hashlib

class MD5Dict:
    def __init__(self, *args, **kwargs):
        self.table = {}
        self.reverse_lookup = {}  # Для хранения оригинальных ключей
        self.update(*args, **kwargs)
    
    def _hash(self, key):
        # Создаем MD5 хэш от ключа
        md5 = hashlib.md5()
        md5.update(str(key).encode('utf-8'))
        return md5.hexdigest()
    
    def __setitem__(self, key, value):
        hashed_key = self._hash(key)
        self.table[hashed_key] = value
        self.reverse_lookup[hashed_key] = key
    
    def __getitem__(self, key):
        hashed_key = self._hash(key)
        return self.table[hashed_key]
    
    def __delitem__(self, key):
        hashed_key = self._hash(key)
        del self.table[hashed_key]
        del self.reverse_lookup[hashed_key]
    
    def get(self, key, default=None):
        hashed_key = self._hash(key)
        return self.table.get(hashed_key, default)
    
    def keys(self):
        # Возвращаем оригинальные ключи
        return self.reverse_lookup.values()
    
    def values(self):
        return self.table.values()
    
    def items(self):
        return [(self.reverse_lookup[k], v) for k, v in self.table.items()]
    
    def pop(self, key, default=None):
        hashed_key = self._hash(key)
        if hashed_key in self.table:
            value = self.table.pop(hashed_key)
            original_key = self.reverse_lookup.pop(hashed_key)
            return value
        if default is not None:
            return default
        raise KeyError(key)
    
    def popitem(self):
        if not self.table:
            raise KeyError('popitem(): dictionary is empty')
        hashed_key, value = self.table.popitem()
        original_key = self.reverse_lookup.pop(hashed_key)
        return (original_key, value)
    
    def setdefault(self, key, default=None):
        hashed_key = self._hash(key)
        if hashed_key not in self.table:
            self.table[hashed_key] = default
            self.reverse_lookup[hashed_key] = key
        return self.table[hashed_key]
    
    def update(self, *args, **kwargs):
        if args:
            if len(args) > 1:
                raise TypeError("update expected at most 1 argument, got %d" % len(args))
            other = args[0]
            if hasattr(other, 'items'):
                for key, value in other.items():
                    self[key] = value
            else:
                for key, value in other:
                    self[key] = value
        for key, value in kwargs.items():
            self[key] = value
        return None
    
    def clear(self):
        self.table.clear()
        self.reverse_lookup.clear()
    
    def copy(self):
        new_dict = MD5Dict()
        new_dict.table = self.table.copy()
        new_dict.reverse_lookup = self.reverse_lookup.copy()
        return new_dict
    
    @classmethod
    def fromkeys(cls, seq, value=None):
        new_dict = cls()
        for key in seq:
            new_dict[key] = value
        return new_dict
    
    def __contains__(self, key):
        hashed_key = self._hash(key)
        return hashed_key in self.table
    
    def __len__(self):
        return len(self.table)
    
    def __repr__(self):
        items = ', '.join(f'{k!r}: {v!r}' for k, v in self.items())
        return f'MD5Dict({{{items}}})'
