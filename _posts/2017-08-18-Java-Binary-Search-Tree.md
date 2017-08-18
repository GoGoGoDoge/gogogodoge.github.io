---
layout: post
title: "Java-Binary-Search-Tree"
date: 2017-08-18
---

## {{page.title}} ##

Hashing is good to find element but it cannot maintain the order of elements. Therefore, we need binary search tree to help us. For balance tree, there are AVL and red-black but it is out of scope of this review.

Even a tree does not require balance, it can be still very difficult to code. Especially the delete part.

Here I sat in the class and taught myself from the following website:

Recursion is our friend.
[Binary Search Tree]: https://www.youtube.com/watch?v=vWchQ0Di7yM&feature=youtu.be&list=PL5iJcUfx7xTejYSchSm1igNJ2M1wcIg0W

### Use case Huffman Coding ###

If we look into standard ASCII, normally it is represented by 7 bits. It might be surprise if we can compress than. The trick lies on the frequency. Some characters actually happen more frequent than others.

Actually it is done by the following steps:
1. Create nodes with two attributes character of course and its frequency
2. Combine lowest two frequency nodes into a tree and sum up their frequency
3. Repeat until all nodes are combined into a tree
4. Label the left branch as 0 and right branch as 1

```
import java.util.*;

// Try to implement huffman coding to save some spaces
public class Huffman_Coding {

    public class Node {
        Character c;
        int freq;
        Node left;
        Node right;
        public Node(Character c, int freq) {
            this.c = c;
            this.freq = freq;
            left = null;
            right = null;
        }
    }

    // Combine two nodes and return
    public Node combine(Node a, Node b) {
        Node root = new Node(null, a.freq + b.freq);
        root.left = a;
        root.right = b;
        return root;
    }

    // Build root from the binary trees
    private void buildCode(Node root, StringBuilder sb, List<String> res) {
        if (root == null) {
            return;
        }
        if (root.c != null) {
            res.add(root.c + ":" + sb.toString());
            return;
        }
        if (root.left != null) {
            sb.append(0);
            buildCode(root.left, sb, res);
            sb.setLength(sb.length() - 1);
        }

        if (root.right != null) {
            sb.append(1);
            buildCode(root.right, sb, res);
            sb.setLength(sb.length() - 1);
        }
    }

    public List<String> huffman (String input) {
        // Basic sanity checck
        if (input == null) {
            return null;
        }

        // Step 1: count the frequency
        int[] alphabet = new int[256];
        for (int i = 0; i < input.length(); i++) {
            alphabet[(int) input.charAt(i)]++;
        }

        // Step 2: construct node base on counting frequency
        Map<Character, Node> map = new HashMap<>();
        for (int i = 0; i < 256; i++) {
            if (alphabet[i] > 0) {
                // System.out.println("debug:" + ((char) i) + ":" + alphabet[i]);
                Node node = new Node((char) i, alphabet[i]);
                map.put((char) i, node);
            }
        }

        // Step 3: Push all candidate into a priority queue
        PriorityQueue<Node> queue = new PriorityQueue<>(new Comparator<Node>(){
            @Override
            public int compare (Node a, Node b) {
                return a.freq - b.freq;
            }
        });
        for (Character c : map.keySet()) {
            queue.offer(map.get(c));
        }

        // Step 4: keep building tree
        while (queue.size() >= 2) {
            Node a = queue.poll();
            Node b = queue.poll();
            Node node = combine(a, b);
            queue.offer(node);
        }

        // Step 5: we only have one node in the queue
        Node root = queue.poll();

        // Step 6: construct the huffman coding for all character
        // Left is 0 and Right is 1

        List<String> resList = new ArrayList<>();

        StringBuilder sb = new StringBuilder();
        buildCode(root, sb, resList);
        return resList;
    }

    public static void main(String[] args) {
        System.out.println("This is a program to write a simple huffman coding");
        String test_case1 = "marco calls maggie";

        Huffman_Coding sol = new Huffman_Coding();
        List<String> res = sol.huffman(test_case1);
        for (String ans : res) {
            System.out.println(ans);
        }
    }
}

```
