# First CrackMe Writeup — Beginner Friendly Reversing Challenges

This is a reverse engineering challenge that can be found [here](https://crackmes.one/crackme/5e45850133c5d4439bb2db3f) (password: crackmes.one). 

Note: This is a write up on how I conducted the sample, which may or may not have been the best method. I am a noob. Hehe XD
## Walkthrough:

The challenge:
``` 
1. Find the serial key and enter it in the textbox
2. Patch the file to always show the Congrats message when you click on button Check. Written in Visual Studio 2017 win32 API.
```

## Inital Analysis
First, running the executable “Crackme1” immediately displays a pop up asking: “Please enter the Serial Key:”

![1](https://user-images.githubusercontent.com/24423222/130875597-03f93eb4-b946-480e-9cac-743e27cfefb6.JPG)
![2](https://user-images.githubusercontent.com/24423222/130875599-50fc4bc9-4e74-4055-8d67-76235a5e357c.JPG)

As a test input “123” we were given an error message: “Wrong serial key. Try again”. 


Lets open IDA to see what's under the hood of the executable. 

When working with samples, my preferred method is to check the “Strings Window” to find any indicators that may help us with the challenge. If you scroll down you will notice a few strings that will grab out attention such as “Congrats!”, “Well done!”, “Wrong serial key. Try again.”. This is similar to what we saw when we entered the wrong serial key.

![3](https://user-images.githubusercontent.com/24423222/130875626-d1d36905-082e-42f2-9df4-ae55121c9d2a.JPG)

Double-clicking on the string brings us to the two functions that display the message box. The left side looks like our goal, which is getting a “Congrats” pop up.

![4](https://user-images.githubusercontent.com/24423222/130875637-2ec76d20-b5aa-482e-a287-68678c05b1a6.png)

If we look at the instructions before the two decisions, we see a test to see if EAX equals EAX. Then immediately a JNZ (jump if not zero) instruction. By setting a breakpoint at JNZ and single stepping through, we see that if EAX is zero, it will redirect us to the wrong serial key path. Single stepping through the code, the incorrect serial key pop up verifies our theory.
![5](https://user-images.githubusercontent.com/24423222/130875663-477aca27-a03b-465d-a0d1-0813eee17f31.png)
![6](https://user-images.githubusercontent.com/24423222/130875665-80c66b84-4ceb-49c7-abd8-c7f892e16af0.png)

Going back to the JNZ instruction we see that RAX (64 bit General Register of EAX) is not zero, its filled with F's. By patching the code and changing the instruction from JNZ to JZ (Jump if zero), it will redirect us to our desired path.

![7](https://user-images.githubusercontent.com/24423222/130875677-0d671c27-1f67-4c99-b506-64413f80e858.png)
![8](https://user-images.githubusercontent.com/24423222/130875693-57f355a9-9fe1-4373-a62d-638c7e31af52.JPG)

However, we are still unable to get our pop-up. Why? If we look at the instructions before MessageBoxA, we see that RAX is still not zero.

![9](https://user-images.githubusercontent.com/24423222/130875729-8ca7f833-e013-4ed5-b4b3-54a2ccc2ecfc.png)

Thus, by changing the value of EAX and set it to zero to fulfill the conditions. Stepping through the code, we now get our "Congrats" message box.![Uploading 1.JPG…]()

 ![10](https://user-images.githubusercontent.com/24423222/130875701-6e5dde0e-4587-4fdc-955b-001678fd28b5.png)
![11](https://user-images.githubusercontent.com/24423222/130875714-37b741a0-f21c-4ed5-bc19-624a81ae4b37.JPG)
