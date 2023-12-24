# Plagiarism_Detector
This Program detects plagiarism using Counting Bloom Filters using a sequence of 4 words at a time (4-grams).
This project was made as part of an assignment for CS110: Problem-Solving with Data Structures and Algorithms course at Minerva University in Fall 2023.

Counting Bloom Filters:
	Bloom filters are probabilistic data structures that check whether a given element is a member of the set or not (Alsayed, 2020). It is probabilistic as its output isn’t always correct and it can give False positive results i.e. misattribute an element as part of the set. Counting Bloom filters (CBFs)  are extensions of Bloom filters that check how many times an element has been in the filter and update the counter array (Jain, 2022). While CBFs provide the functionality of counting the frequency of elements, they require more storage. For each bit of data in BF, we would use a byte (8 bits) in CBF. 
	Bloom filters use Hash tables. A hash table is a data structure (an extension to arrays) that takes a key-value pair and uses the key to insert, search, and delete values in constant runtime O(1) (Ha Tran Nguyen & Tran, 2023). Since a hash table is an extension of an array, we need to assign/ generate an index for each element’s position. We use hash functions to generate an index from the key of the hash table.
	Hashing is the process of generating a value from a text or numbers using a mathematical function (Jain, 2023). The mathematical functions used for hashing are called hash functions. An example of a hash function is;
hash-value=len(input)  mod  bloom-filter-length
This is a hash function that takes the length of the input and gets its remainder when divided by the length of the bloom filter. A bloom filter’s length is the number of buckets in the filter or the number of indices in that array. Note, that CBFs or Bloom filters are at core arrays, but with special characteristics. There are a few characteristics that an ideal hash function should have explained by Heymsfeld (2018);
A small change in input changes the output significantly. 
Regardless of the length of the input, the output is of the same length.
The hash is irreversible. This means we can find the hash value of any string or number but we cannot find the given string or number from the hash value. 
A hash function is collision-free. A collision occurs when two different inputs to a hash function produce the same output. However, this should not be the case in an ideal hash function.
Supported Operations:
CBFs support three main operations. Hashing is another operation that CBFs support but that is used in all these operations so it’s not included as a separate operation. 
Insert: 
Insert operation adds an input to the CBF (the filter). This does not directly append the input but uses hashing. One or multiple hash function(s) are used in the hash operation. Each hash function gives an index. For example, if this is an initial CBF with filter size 10;
[0,0,0,0,0,0,0,0,0,0]

All the indices will be initialized to 0. If we use an input “Hello World” and one hash function;
hash-value=len(input)  mod  bloom-filter-length,
we will get the hash value of 11 mod 10 i.e. 1. Note this is the first index (0th index in Python). Here, we only have one hash function so we will only get one index. The number of hash values we get linearly scales with the number of hash functions. In Bloom filters, we would replace the 0s at the output hash’s indices with 1s. However, in CBFs we will keep adding 1 every time we encounter that index. So, the CBF will be updated as
[1,0,0,0,0,0,0,0,0,0]

The time complexity of this operation is O(k), where k is the number of hash functions. This is because it needs to calculate the hash value for each hash function and update the corresponding index in the filter.
Search: 
The search operation also takes a string or a number as input. Similar to  Insert, it finds the hash values of the string using all the hash functions. This then returns the hash values. We check those indices for value in the CBF. If those indices have values, then the element exists. Let’s search for the item “Hello World” in the CBF. Using the hash function as previously,
hash-value=len(input)  mod  bloom-filter-length,
we will get the hash value of 1. We have the following CBF;
[1,0,0,0,0,0,0,0,0,0]

The index 1 (0 in Python) has a non-zero element. Hence, the search will return True. However, we need to note that CBFs are probabilistic data structures so they will either return False or probably True. If a search function returns True, it might not be alwaysTrue. Let’s look at that example. 
	We will look if this CBF has the element “Minerva University 26”. The character count is 21 so the hash function will return 21 mod 10 i.e. 1. The CBF has a non-zero element at the index 1 so the search operation will return a True (False Positive).
Similar to insert, the time complexity of the search operation is O(k), where k is the number of hash functions. This is because it needs to calculate the hash value for each hash function and check the corresponding index in the filter.
Delete: 
The delete operation removes an item from the set. This operation is only supported in CBFs and not in Bloom filters. This is because even if we added 10 items, we would still have 1 as the index and there is no information about frequency. If we try deleting one, we would have to replace 1 with 0 in that index of BF, which is not accurate. The delete operation is executed similarly to the insert and search operations. First, a hash value is calculated for the item. Then, we look for those indices and reduce 1 from each index. In the example, we currently have this CBF;
[1,0,0,0,0,0,0,0,0,0]

If we add another item here, “Hello Nepal”, it will give the index of 1 so the CBF will be updated as;
[2,0,0,0,0,0,0,0,0,0]

Now, we will delete “Hello World” from the CBF. It has 11 characters. Hence, the hash function will return 11 mod 10 i.e. 1. Now, we look at the 1st index and reduce 1 from the value;
[2,0,0,0,0,0,0,0,0,0]

The time complexity of the delete operation is also O(k), where k is the number of hash functions. This is because it needs to calculate the hash value for each hash function and update the corresponding index in the filter.
Generally, we keep the number of hash functions constant. Hence, it will have a constant scaling or O(1). Also, the space complexity of counting Bloom Filters is O(m). Here, m is the length of the filter or number of indices in that base array. 
Applications of CBFs:
CBFs can check if an element is a member of a set or not in almost constant runtime. This is a very useful feature to have in data processing and CBFs are very efficient data structures to use here. These are a few use cases:
Membership Verification:
This checks if a given person is part of an organization's member list. This feature is useful in many instances. We could link member IDs to their identity card’s chip and whenever they tap, we can try checking if they are in the member list. If they are we could give them access to the building or other resources.
Email Filtering:
These days, almost every website requires us to sign in or give our email as personal information. However, not all of them might be safe. Hence, we could use CBFs to store trusted email addresses and only get emails from them. All other emails can be filtered and stored in spam. This is specifically useful when using a work/ school account. We could use the organizational email directory as the set and only receive emails from people within that directory.
Spell Check:
This one is very useful for students or in general people writing formal papers/ documents. We could add all the data from a dictionary as the global set (insert in the CBF). Then, we can search for texts from the document where the user is typing. If that text is not found in the original set, we can underline that in red to signal that it is an error. We could suggest elements with the highest similarity score as possible corrections. 

Python Implementation: 
I created a Python Class ‘CountingBloomFilter’ with the number of hash functions and filter length as parameters. 
Hashing: (hash_cbf function)

Figure 1. Python Implementation of the hash function.
I used the number of hash functions as a parameter. This method is designed to make as many hash functions as required by the users. If there is only one hash function to be made, the hash function will be;
hash-value=len(input item)  mod  filter-length
This follows some properties of an ideal hash function like giving the same length range (0 to the length of filter) for any input size. However, we can get the same hash values for multiple items like the example discussed in the search function above. It still has an advantage over the other hash functions I used. It will take O(1) runtime as it only takes the length of an item and does one modular division.
If the number of hash functions is greater than one, then the hash value is calculated as follows;
hash-value =(sum(ord(c)* (indexhash-function) for index, c in enumerate(item))) % self.filter_length
This formula finds the ASCII value of input characters and finds the exponent of the index of the character to the hash function. We finally find the remainder as the hash value. This is to ensure the hash value remains inside the range of CBF. This takes a runtime of O(c), where c is the number of characters in the item. This will give a significantly different value for even a small change in input. This is because ASCII code for different characters will be different and the exponents change rapidly hence will be a huge difference.

To find the number of hash functions to use, I used the following formula
k = (m/n) * ln(2) 		(Github Copilot & Jain, 2023)
Search:

Figure 2. Python Implementation of the ‘search’ method.
The ‘search’ method is used to query the membership of an item in the CBFs. It takes an item as an input and returns a boolean value indicating whether the item is possibly in the set.
I called the hash_cbf function to find the hash value of the given item/element. This will return a list of hash values one of each hash function. Next, the method will iterate over each hash value. For each hash value, it checks the corresponding index in the filter. If the count at that index is 0, it means that no item that hashes to that index has been inserted into the filter, so the method immediately returns ‘False’, indicating that the item is definitely not in the set.
If the count at all the indices corresponding to the hash values is greater than 0, the method returns ‘True’, indicating that the item is possibly in the set. However, this could be a false positive, as different items can hash to the same indices. The time complexity of the ‘search’ method is O(k), where k is the number of hash functions. 

Insert:

Figure 3. Python Implementation of the ‘insert’ method.
The insert method starts by calculating the hash values for the item using the ‘hash_cbf’ method. This will return a list of hash values, one for each hash function. Then, it iterates over hash values, and for each hash value, it increments the count at the corresponding index in the filter.
Delete:

Figure 4. Python Implementation of the ‘delete’ method.
Similar to the insert method, the method calculates the hash values for the item using the ‘hash_cbf’ method and iterates over each hash value. For each hash value, it decreases the count at the corresponding index in the CBF by 1. 




Calculate_fpr:

Figure 5. Python Implementation of the ‘calculate_fpr’ method.
As per (Kirsch & Mitzenmacher, 2008), the False Positive Rate for a Bloom Filter is;
f ≈ (0.6185) mn
Here, f is the False positive rate, m is the number of bits in the filter, n is the number of elements added in the filter. This is an estimate based on the assumption that we will use the optimal number of hash functions. However, the number of hash functions is a parameter for my program and users might not give the best parameters. Hence, we will use the more sophisticated version of the formula;
			f =(1− e^(kn/m))^k
Here, k is the number of hash functions used.
		
Calculate_memory_size:

Figure 6. Python Implementation of the ‘calculate_memory_size’ method.
I solved the formula to find FPR to find the memory size. Note that, I am using the filter size as a proxy for memory size. We know that each index will store one byte of data (or a bit for BF). When the filter size increases, the memory size increases in the same ratio too.

CBF Efficiency:
Scaling of Memory Size with FPR:
	
Figure 7. Scaling of Memory size with False Positive rate. The false positive rate is a ratio (probability) and hence is unitless. The memory size is represented by the size of the CBF, which acts as a proxy. It is measured as the number of elements in the CBF or the total length. In real life, Memory size will be measured in bits/ bytes. Length 1 of CBF is equivalent to 1 Byte of memory.
CBFs are probabilistic data structures. While searching for an element in a set, it will either return ‘False’ or ‘Probably yes’. It gives an advantage in time complexity while compromising the FPRs. When we increase FPR, it decreases the memory size. When we are willing to accept more FPRs (an estimated data is enough), this will save memory. However, it does not happen linearly. For the first 0.15 FPR, the Memory size reduces drastically. The slope of the graph reduces meaning smaller change with FPR until when FPR is 0.8 and the rate of change increases when FPR is above 0.8 hence a sudden drop again. This shows that an FPR at around 0.2 looks optimal as it uses half the memory size than that of FPR 0 and can still be a really good estimate. 
Scaling of Memory Size with Number of Inputs:

Figure 8. Scaling of Memory size with the Number of Items stored. The Number of items stored is unitless. The memory size is represented by the size of the CBF, which acts as a proxy. It is measured as the number of elements in the CBF or the total length. In real life, Memory size will be measured in bits/ bytes. Length 1 of CBF is equivalent to 1 Byte of memory.
Memory size is linearly dependent on number of items stored. This is not intuitive as each item added will get k number of indices and they will be in between the first and last index of the CBF. However, an important assumption here is that the FPR is constant. Hence, to keep that FPR constant, we need to change the memory size with the change in number of items stored.
Scaling of FPR with Number of Hash Functions:

Figure 9. Scaling of False Positive Rate with Number of Hash functions. The false positive rate is a ratio (probability) and hence is unitless. The number of hash functions represents the amount and does not have a unit. 
With a constant memory size, the FPR decreases initially as a random task won’t return as True when it accidentally matches just one index. However, after a certain time (when the number of hash functions is very close to memory size). Memory size is 50 here, and the FPR starts increasing quadratically (O(n^2)) and then reduces the slope from around 80 (O(nlogn)). This eventually forms a horizontal asymptote with the line y=1. This is because, with very high hash functions, the FPR will be close to 1 for a representative sample but will never reach 1 as there might be some elements that are actually in the set.
Also, the choice of filter length (memory size) changes the way the graph looks but the scaling will be the same as described above.


Access Time with Number of Items Stored:

Figure 10. Scaling of access time with the Number of Items stored. The Number of items stored is unitless. Access time for an item is defined by the time taken to implement the search() method from the CountingBloomFilter() class. A random element was searched. The code is in the appendix. The unit for access time is seconds.
	In this graph, the FPR is not kept constant. The access time for one element should remain constant when the number of items stored increases when the number of hash functions is constant. The scaling of the search method is o(k) where k is the number of hash functions. However, in the figure, the FPR is not constant so it changes with the change in the number of items stored and makes this linear connecting between items stored and access time. 
While comparing access time and number of items stored, we had to keep the FPR constant. However, when we change the number of items stored, FPR changes as well.  
f =(1− eknm)k
Here, the FPR (f) is related to the number of items stored (n). To keep the FPR constant, I tried adjusting the filter length and the number of hash functions in the same ratio as the number of items stored. However, it took a long time as the number of items stored is 124787 even while splitting as lines.

Figure 11. Increase of filter length and number of hash functions to mitigate the effects of change in the number of items stored in order to keep the FPR constant.


Plagiarism:
Plagiarism is copying someone’s work without properly attributing it (citations). This application detects plagiarism which is significant as it ensures authors get credit for the work they do. In this program, we will only be concerned about plagiarism for text. In this case, plagiarism will be copying someone’s text entirely, using ideas without citations, and using text after paraphrasing. This program can only detect cases of plagiarism where the entire text or portion of text is copied from the available sources.
Working with the sequence of characters is useless here as the English alphabet only has 26 characters and some symbols. In that case, everything will be deemed plagiarized. Testing for one word at a time is another option but this has the same issue as using characters. Despite having an advantage in searching the elements faster, this will take a much longer time to store the entire input (source) one word at a time. Also, this will not yield accurate results as essentially everyone will have words from a dictionary. If a dictionary was part of the source, everything would be deemed plagiarized. 

Figure 12. Left: Time taken to detect plagiarism comparing one word at a time (1-gram).
Right: Time taken to detect plagiarism comparing 10 words at a time (10-gram). The unit of time is seconds.

So, a better solution would test for a sequence of words together, often known as n-grams. An n-gram is a contiguous sequence of n items from a given sample of text or speech. Hence, I will define plagiarism as the presence of identical n-grams in two different texts. Having a larger ‘n’ ensures that we don’t get False positives easily and will reduce the misattribution of common phrases as plagiarism. However, it will miss chases of plagiarism when short phrases are used or the sentence is paraphrased. 
While using a smaller ‘n’ value we will have higher instances of misattribution but will capture instances of plagiarism for short sentences/ phrases. For example, in the sentence "I love to play football", the 2-grams would be: "I love", "love to", "to play", or "play football". If someone else says “I love you XYZ”, the 2-grams would be: "I love", "love you", "you XYZ". Here, "I love" and  "love you" will appear as plagiarized but they are not.
In my program, I will use a 4-gram set of words, to balance out these two effects and get the best out of both worlds. I got this number after testing with test cases from Shakespeare's works.


CBFs for Plagiarism Detector:
CBFs can store and search data efficiently which makes them suitable to use for plagiarism detection. They provide the following advantages;
Space Efficiency:
CBFs are more space-efficient than other data structures like hash tables or sets.  Since they use hashing and only store a few bytes of data, they can store huge information in limited memory.

Time Efficiency: 
The time complexity for insertions and search in a CBF is O(k), where k is the number of hash functions. The number of hash functions is constant and a small number, which means it takes constant time regardless of the number of elements in the CBF. This makes CBFs suitable for applications that require fast responses.
Ability to Count and Delete:
Since we are using CBFs and not just standard Bloom filters, we can keep track of the frequency of elements which can tell how frequently an element is plagiarized. This can also be useful to delete elements when sources are withdrawn or deleted. 


Time Complexity using Plagiarism Detector:
	
Figure 13. The time required by the plagiarism detector to detect plagiarism using Counting Bloom Filters for an increasing number of items.
Theoretically, detecting plagiarism and adding text to the filter should have O(nk) runtime. Here, n is the number of n-grams or number of elements, and k is the number of hash functions. This data is not represented in the experimental analysis because we have used a small k size and k is constant. Hence, this gives us almost constant time in terms of k. We only have n component inside. Hence, the time taken to add elements or detect plagiarism is equivalent to the number of n-grams/ elements i.e. it has a linear scaling (O(n)).

Limitations:
False Positives:
While CBFs come with a great saving of time and memory, they can produce False positive results i.e. a non-plagiarized document might be incorrectly identified as plagiarized. However, it can be controlled by adjusting the size of the filter and the number of hash functions. To reduce the False positive rates, I have used appropriate formulas (functions) to determine the ideal number of hash functions and filter size from trustable sources on the web.
No Guarantee of Originality:
While the plagiarism detector will return definitely not plagiarized, it might not be certain. This depends on the n-value used. For a larger n-value, it cannot identify smaller phrases plagiarized.
Paraphrasing:
It is the property of an ideal hash function that even a small change in input will bring a big difference in the hash value. Hence, paraphrased texts will not be detected as plagiarized in a plagiarism detector made using CBFs.
Copy of Ideas:
Since, this detector only uses the sequence of texts, using synonyms can fool the plagiarism detector while still using the author’s ideas.


Alternatives:
Arrays:
Using arrays is a brute-force technique to detect plagiarism. To insert an element in the array, it would require O(1) time however if it’s inserting non-duplicate elements, it will take O(n) time as we have to compare with all the existing elements. To detect an element, it will take O(n) time where n is the length of the array. This is computationally expensive than CBFs which take O(k) time ~ O(1).
Hash tables:
We could use Hash tables as an alternative to CBFs. However, it will take more storage as we will have to store all the hash values for each element separately, while we are storing them in a fixed filter size in CBFs. The time complexity to search and insert will be O(1) but in the worst case will be O(n) if all the keys have the same index.



References
Alsayed, K. (2020, February 12). What are Counting Bloom Filters? | Analytics Vidhya. Medium. Retrieved December 6, 2023, from https://medium.com/analytics-vidhya/cbfs-44c66b1b4a78
Ha Tran Nguyen, P., & Tran, Q. (2023, June 16). All you need to know about hashing (to build a plagiarism detector). Google Drive. Retrieved December 6, 2023, from https://drive.google.com/file/d/1aYkzYNQK74h3EN0StZAqm7e_8ptFdens/view
Heymsfeld, R. (2018, July 17). Hash Functions. CertMike. Retrieved December 11, 2023, from https://www.certmike.com/hash-functions/
Jain, S. (2022, May 20). Counting Bloom Filters - Introduction and Implementation. GeeksforGeeks. Retrieved December 6, 2023, from https://www.geeksforgeeks.org/counting-bloom-filters-introduction-and-implementation/
Jain, S. (2023, March 9). Hash Functions and list/types of Hash functions. GeeksforGeeks. Retrieved December 6, 2023, from https://www.geeksforgeeks.org/hash-functions-and-list-types-of-hash-functions/
Jain, S. (2023, July 6). What is Hashing? GeeksforGeeks. Retrieved December 6, 2023, from https://www.geeksforgeeks.org/what-is-hashing/
Jain, S. (2023, September 13). Bloom Filters - Introduction and Implementation. GeeksforGeeks. Retrieved December 13, 2023, from https://www.geeksforgeeks.org/bloom-filters-introduction-and-python-implementation/
Kirsch, A., & Mitzenmacher, M. (2008, May 15). Less hashing, same performance: Building a better Bloom filter. Harvard University. Retrieved December 12, 2023, from https://www.eecs.harvard.edu/~michaelm/postscripts/rsa2008.pdf

