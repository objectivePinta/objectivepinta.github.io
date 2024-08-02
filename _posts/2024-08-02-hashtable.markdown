---
layout: post
title:  "Hashtable"
date:   2024-08-02 19:51:30 +0100
tags: java hashtable
categories: java-basics
---

In this post, I'll play with a hashtable, which I totally forgot what it is. 
So to rewind a bit, a hashtable is basically a map in Java, which stores data structured into keys and values.
For example, a map of the ages of the guests at your party, would be something like: Andy -> 33, Mary -> 38, Johny -> 40, etc. 
The tricky part is that the key has to be unique, otherwise it would be overwritten by the second entry with the same key.
Now we want to look deeper into how this is done. How is this data actually stored in a map, and not any kind of map, but a Hashtable. 
The `Hashtable` is in `java.util` package and is there since version 1.0 (completely deprecated and replaced by HashMap from java 1.2).

### Where does the hashtable stores its data? 
The hashtable is backed by a linked list. The linked list is a list of nodes, where each node contains a value and besides that contains a reference to the next node in the list. Something like in the picture below: 

![Employees table](/pictures/hashtable/diagrama.png)

If we go through the linked list, from node to node, we will know we have reached its end because the end (Coada, which in romanian means both Queue and tail) stores nothing in the reference to the next node. 

{% highlight java %}

public class Node<K, V> {

  private K key;
  private V value;
  private Node<K, V> next;

  public Node(K key, V value) {
    this.key = key;
    this.value = value;
  }

}
{% endhighlight %}

This node data will be stored in the `key` and `value` variables (which we need for the map), while the reference to the next node will be stored in the `next` variable. 

### Hashtable

So the Hashtable, which is simplified here, is backed up by an array of nodes. Each node in the array is the head (Cap in my romanian diagram) of a linked list. Also each node (and its linked list attached) is a called a bucket. 

{% highlight java %}

public class Hashtable<K, V> {

  private static final int INITIAL_CAPACITY = 16;
  private Node<K, V>[] table;

  public Hashtable() {
    table = new Node[INITIAL_CAPACITY];
  }

  private int getIndex(K key) {
    int hash = key.hashCode();
    /*
    0x7FFFFFFF is 0111 1111 1111 1111 1111 1111 1111 1111 : all 1 except the sign bit.
    (hash & 0x7FFFFFFF) will result in a positive integer.
    (hash & 0x7FFFFFFF) % tab.length will be in the range of the tab length.
*/
    return (hash & 0x7fffffff) % table.length; 
  }

  public void put(K key, V value) {
    int index = getIndex(key);
    Node<K, V> newNode = new Node<>(key, value);
    if (table[index] == null) {
      table[index] = newNode;
    } else { //if it's not null, so it's a head of a linked list we go through the linked list
      Node<K, V> current = table[index];
      Node<K, V> prev = null;
      while (current != null) { // we go as far as the end of the queue, current becomes in the last line of the while the reference of the current node
                                //...until there's no reference
        if (current.getKey().equals(key)) { //if we find the key in a one of the nodes,
          current.setValue(value); //we overwrite the value 
          return;
        }
        prev = current;
        current = current.getNext();
      }
      prev.setNext(newNode); //here we're sure prev is the end of the queue and now we add the new node after it
    }
  }

  public V get(K key) {
    int index = getIndex(key);
    if (table[index] == null) {
      return null;
    } else {
      Node<K, V> nodeAtIndex = table[index]; // here we look for the head of the linked list where we should start our search
      while (nodeAtIndex != null && !nodeAtIndex.getKey().equals(key)) { //we go through the linked list until there's nothing
                                                                         //or we stop if we find that the current node has the same key as the one searched for
        nodeAtIndex = nodeAtIndex.getNext();
      }
      if (nodeAtIndex != null && nodeAtIndex.getKey().equals(key)) { // if the found node is not null and really has the key the user provided, we have it
        return nodeAtIndex.getValue();
      } else {
        return null;
      }
    }
  }

}

{% endhighlight %}


