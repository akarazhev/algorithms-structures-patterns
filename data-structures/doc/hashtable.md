# The Hashtable Data Structure

In computing, a hash table (hash map) is a data structure that implements an associative array abstract data type, 
a structure that can map keys to values. A hash table uses a hash function to compute an index, also called a hash code, 
into an array of buckets or slots, from which the desired value can be found.

Ideally, the hash function will assign each key to a unique bucket, but most hash table designs employ an imperfect 
hash function, which might cause hash collisions where the hash function generates the same index for more than one key. 
Such collisions are always accommodated in some way.

## Characteristics

- Abstract data type
- Provide access to data using keys
- Key does not have to be an integer
- Consists of key/value pairs - data types don't have to be match
- Optimized for retrieval (when you know the key)
- Associative array is one type of hash table

## Operation and time complexity

Hashing 

* Maps keys of any data type to an integer
* Hash function maps keys to int
* In Java, hash function is Object.hashCode()
* Collision occurs when more than one value has the same hashed value

Load Factor

* Tells us how full a hash table is
* Load factor = # of items / capacity = size / capacity
* Load factor is used to decide when to resize the array backing the hash table
* Don't want load factor too low (lots of empty space)
* Don't want load factor too high (will increase the likelihood of collisions) 
* Can play a role in determining the time complexity for retrieval

Add to a Hash Table backed by an array

1. Provide a key/value pair
2. Use a hash function to hash the key to an int value
3. Store the value at the hashed key value - this is the index into the array

Retrieve a value from a hash table

1. Provide the key
2. Use the same hash function to hash the key to an int value
3. Retrieve the value stored at the hashed key value

Linear Probing implementation:

```java
final class SimpleHashtable {
    private KeyValuePair[] hashtable;

    SimpleHashtable() {
        hashtable = new KeyValuePair[10];
    }

    void put(final String key, final Employee value) {
        var hashedKey = hashKey(key);
        if (occupied(hashedKey)) {
            final var stopIndex = hashedKey;
            if (hashedKey == hashtable.length - 1) {
                hashedKey = 0;
            } else {
                hashedKey++;
            }

            while (occupied(hashedKey) && hashedKey != stopIndex) {
                hashedKey = (hashedKey + 1) % hashtable.length;
            }
        }

        if (occupied(hashedKey)) {
            System.out.println("There's already a value at the position " + hashedKey);
        } else {
            hashtable[hashedKey] = new KeyValuePair(key, value);
        }
    }

    Employee get(final String key) {
        final var hashedKey = findKey(key);
        if (hashedKey == -1) {
            return null;
        }

        return hashtable[hashedKey].getValue();
    }

    Employee remove(final String key) {
        final var hashedKey = findKey(key);
        if (hashedKey == -1) {
            return null;
        }

        final var value = hashtable[hashedKey].getValue();
        hashtable[hashedKey] = null;

        final var oldHashtable = hashtable;
        hashtable = new KeyValuePair[oldHashtable.length];
        for (final var keyValuePair : oldHashtable) {
            if (keyValuePair != null) {
                put(keyValuePair.getKey(), keyValuePair.getValue());
            }
        }

        return value;
    }

    private int hashKey(final String key) {
        return key.length() % hashtable.length;
    }

    private int findKey(final String key) {
        int hashedKey = hashKey(key);
        if (hashtable[hashedKey] != null && hashtable[hashedKey].getKey().equals(key)) {
            return hashedKey;
        }

        int stopIndex = hashedKey;
        if (hashedKey == hashtable.length - 1) {
            hashedKey = 0;
        } else {
            hashedKey++;
        }

        while (hashedKey != stopIndex && hashtable[hashedKey] != null && !hashtable[hashedKey].getKey().equals(key)) {
            hashedKey = (hashedKey + 1) % hashtable.length;
        }

        if (hashtable[hashedKey] != null && hashtable[hashedKey].getKey().equals(key)) {
            return hashedKey;
        } else {
            return -1;
        }

    }

    int size() {
        var size = 0;
        for (final var keyValuePair : hashtable) {
            if (keyValuePair != null) {
                size++;
            }
        }

        return size;
    }

    private boolean occupied(final int index) {
        return hashtable[index] != null;
    }

    @Override
    public String toString() {
        final var builder = new StringBuilder();
        for (var i = 0; i < hashtable.length; i++) {
            if (hashtable[i] == null) {
                builder.append("Empty");
            } else {
                builder.append("Position ").append(i).append(": ").append(hashtable[i].getValue());
            }
        }

        return builder.toString();
    }

    private final static class KeyValuePair {
        private final String key;
        private final Employee value;

        KeyValuePair(final String key, final Employee value) {
            this.key = key;
            this.value = value;
        }

        String getKey() {
            return key;
        }

        Employee getValue() {
            return value;
        }
    }
}
```

It can be used as follow:

```java
final var janeJones = new Employee(123, "Jane", "Jones");
final var johnDoe = new Employee(4567, "John", "Doe");
final var marySmith = new Employee(22, "Mary", "Smith");
final var mikeWilson = new Employee(3245, "Mike", "Wilson");
// final var billEnd = new Employee(78, "Bill", "End");

final var hashtable = new SimpleHashtable();
hashtable.put("Jones", janeJones);
hashtable.put("Doe", johnDoe);
hashtable.put("Wilson", mikeWilson);
hashtable.put("Smith", marySmith);
System.out.println(hashtable);
var size = hashtable.size();

var value = hashtable.get("Wilson");

value = hashtable.get("Smith");

hashtable.remove("Wilson");
hashtable.remove("Jones");
System.out.println(hashtable);
size = hashtable.size();

value = hashtable.get("Smith");
```