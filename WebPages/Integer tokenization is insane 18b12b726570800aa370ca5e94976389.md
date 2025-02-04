# Integer tokenization is insane

Type: Blog
Link: https://www.beren.io/2023-02-04-Integer-tokenization-is-insane/

# Integer tokenization is insane

Tokenizers’ problems  with integers. When tokenizing intergers, it splits and merges digits of the same number (BPE algorithm). 

This image (1st) below shows digits tokenized by a unique token. I.e., any operation using those digits imply handling a spacial case (like do we but using only 0-9). Also unique encoding for what seems to be dates between 1900 and 2000.

The second image shows how the numbers as splitted into two tokens (i.e. 1-3 for *2249* tokenized as ‘2’ and ‘249’)

![image.png](Integer%20tokenization%20is%20insane%2018b12b726570800aa370ca5e94976389/image.png)

![image.png](Integer%20tokenization%20is%20insane%2018b12b726570800aa370ca5e94976389/image%201.png)

References:

- Gage, Philip (1994). ["A New Algorithm for Data Compression"](http://www.pennelynn.com/Documents/CUJ/HTML/94HTML/19940045.HTM). *The C User Journal*.