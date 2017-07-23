---
layout: "post"
title: "Median of Two Sorted Arrays"
date: "2017-07-22 17:06"
---

# Question:
>There are two sorted arrays arrayA and arrayB of size m and n respectively.
> 
>Find the median of the two sorted arrays. The overall run time complexity should be O(log (m+n)).

### Median
> The median of a finite list of numbers can be found by arranging all the numbers from smallest to greatest.
> If there is an odd number of numbers, the middle one is picked. For example, consider the set of numbers
> 1, 3, 3, 6, 7, 8, 9
> This set contains seven numbers. The median is the fourth of them, which is 6.
> If there are an even number of observations, then there is no single middle value; the median is then usually defined to be the mean of the two middle values.[1][2] For example, in the data set
> 1, 2, 3, 4, 5, 6, 8, 9
> the median is the mean of the middle two numbers: this is (4 + 5) ÷ 2, which is 4.5.

### Example 1:
> arrayA = [1, 3]
> arrayB = [2]
> The median is 2.0

### Example 2:
> arrayA = [1, 2]
> arrayB = [3, 4]
> The median is (2 + 3)/2 = 2.5



### Means:
> ArrayA = [A1, A2...Am], A1<A2<...<Am
> ArrayB = [B1, B2...Bn], B1<B2<...<Bn
> Findout the median number in ArrayA or ArrayB

# Thinking
Oh Shit! What the fuck question! i am just a stupid man which i just can answer the question like this

> ArrayA = [10, 20]
> ArrayB = [30]

ez, i found the answer is 20, great! i like this question, mom tolded me simple thing is always the best.

but that's too simple, i need some challenge.

> ArrayA = [10, 20, 30, 40, 50, 60, 70, 80, 90, 100, 110]
> ArryaB = [120, 130, 140, 150, 160]

Aha! i found the answer is (80+90)/2 = 85 in seconds

but why! how do i got the answer step by step? Hmmm..
1. the total number is 11+5=16, so i need find the 8th, 9th number in ArrayA and ArrayB
2. the 8th, 9th number in ArrayA is 80 and 90, now i compare 90 with 120 which is the smallest one in ArrayB, yes 90<120, so the 8th, 9th in ArrayA and ArrayB is 80 and 90
3. so answer is (80+90)/2=85

Wow! i got the answer and i fell very happy, i need more happy! Now, i change the question with ArrayB:

> ArrayB = [51, 61, 71, 81, 91]

So, i did the same thing step by step:
1. the total number is 11+5=16, so i need find the 8th, 9th number in ArrayA and ArrayB
2. the 8th, 9th number in ArrayA is 80 and 90, now i compare 90 with 51 which is the smallest one in ArrayB, 90>51, Hmmm.., i lost the answer, but i am very clear that 90 can't be the 9th number in ArrayA and ArrayB, i need do more steps now.
3. Thinking..., Thinking..., Thinking...
4. i divide ArrayB into two Array, ArrayB1 = [51, 61, 71], ArrayB2 = [81, 91], and i also divide ArrayA into two Array, ArrayA1 = [10, 20, 30, 40, 50], ArrayA2 = [60, 70, 80]. Why i divide the two array like this?
> i lost 8th, 9th number, i don't know which one is, but i know it must't be in [90, 100, 110], so i got the new ArrayA = [10, 20, 30, 40, 50, 60, 70, 80], ArrayB = [51, 61, 71, 81, 91]
>
> one solution is compare the largest one in ArrayA and the smallest one in ArrayB, it will find the answer or remove them and then compare again one by one until find the answer, but it will be slow, i need a faster solution, so i will not find one by one, i will find half by half
>
> divide ArrayB into ArrayB1 = [51, 61, 71] and ArrayB2 = [81, 91], why not divide half of ArrayA? you will find the answer later.
> how about ArrayA1 and ArrayA2? i don't know which one is 8th, 9th, but [90, 100, 110] can't be, we make them dirty numbers, can we make more numbers dirty?
>
> as we know all of ArrayA1 is smaller than ArrayA2 and all of ArrayB1 is smaller than ArrayB2, if the total numbers in ArrayA and ArrayB1 is 8, compare the largest one called MaxA1 in ArrayA1 and called MaxB1 in ArrayB1
> if MaxA1<MaxB1, MaxA1 will smaller than all of ArrayB2, the total numbers of ArrayA2 and ArrayB2 is 9, with MaxB2 there will be 10 numbers larger than MaxA1, so MaxA1 is at most the 7th, that means all of the ArrayA1 can't be the 8th, so we are successful in making all of the numbers in ArrayA1 dirty.
> if MaxA1>MaxB1, we will make all of the numbers in ArrayB1 dirty.
> if MaxA1==MaxB1, lucky boy, you find the 8th, and the 9th will be the smallest one in ArrayA2 and ArrayB2.
>
> otherwise all of ArrayA2 or ArrayB2 are dirty numbers by compare the smallest one called MinA2 in ArrayA2 and called MinB2 in ArrayB2, because they can't be the 9th.
> If we need make the total numbers in ArrayA1 and ArrayB1 is 8, we must divide half of the smaller Array to make sure.
> So, ArrayA will be divide into ArrayA1 = [10, 20, 30, 40, 50] and ArrayA2 = [60, 70, 80]
5. compare the largest one in ArrayA1 and ArrayB1, 50<71, all of ArrayA1 are dirty numbers.
6. compare the smallest one in ArrayB1 and ArrayB2, 60<81, all of ArrayB2 are dirty numbers.
7. at the moment we got some new information, the 8th and 9th must be in the new ArrayA = [60, 70, 80], ArrayB = [51, 61, 71], and the numbers which is smaller than the 8th is 5([10, 20, 30, 40, 50]), so we are clear the 8th, 9th in the old Array are the 3rd and 4th in the new Array.
8. divide ArrayB into ArrayB1 = [51, 61], ArrayB2 = [71], divide ArrayA1 into ArrayA1 = [60], ArrayA2 = [70, 80], 61>60 and 70<71, so the new ArrayA=[70, 80], ArrayB=[51, 61], and the 8th, 9th in the old Array are the 2nd and 3rd in the new Array.
9. divide ArrayB into ArrayB1=[51], ArrayB2=[61], divide ArrayA into ArrayA1=[70], ArrayA2=[80], 70>51 and 61<80, so the new ArrayA=[70], ArrayB=[61, and the 8th, 9th in the old Array are the 1st and 2nd in the new Array..
10. now we are clear that the 8th = 61, the 9th = 70, so the answer is (61+70)/2 = 65.5

#### Well, we found the answer again, smile one and saw some beautiful woman.

According to our analysts we will get some useful information
- divide the smller array half by half
- make the total numbers of ArrayA1 and ArrayB1 equal to the new median number's order to make sure you can make all of ArrayA1 or ArrayB1 dirty.
- if you found MaxA1==MaxB1 or MinA2==MinB2, you are luck to find the answer.
- as you divide the smaller array half by half, at last the new smaller array size will be 1. i think that will be the simple question.


# Answer
{% highlight cpp %}
class Solution
{
    public:
        double findMedianSortedArrays(vector<int>& arrayA, vector<int>& arrayB) {
            int m = arrayA.size();
            int n = arrayB.size();
            if (m<n) return findMedianSortedArrays(arrayB, arrayA);
            int median1 = (m+n+1)/2-1;
            int median2 = (m+n+2)/2-1;  //if (m+n)%2==1, median2==median1
            if (n==0) return (arrayA[median1]+arrayA[median2])/2.0;
            if (m>n && arrayA[median2] <= arrayB[0])
            {
                return (arrayA[median1] + arrayA[median2]) / 2.0;
            }

            int minA_index = 0;
            int minB_index = 0;
            int sizeA = (m+n+1)/2;
            int sizeB = n;
            while(1)
            {
                int divide_num = (sizeB+1)/2;
                int maxB1 = arrayB[(divide_num + minB_index - 1)];
                int minB2 = arrayB[(divide_num + minB_index)];

                int maxA1 = arrayA[sizeA-divide_num+minA_index-1];
                int minA2 = arrayA[sizeA-divide_num+minA_index];

                if (sizeB==1)
                {
                    if (sizeA == 1)
                    {
                        if (median1 == median2)
                        {
                            return arrayA[minA_index]>arrayB[minB_index]?arrayB[minB_index]:arrayA[minA_index];
                        }
                        else
                        {
                            return (arrayA[minA_index]+arrayB[minB_index])/2.0;
                        }
                    }
                    else
                    {
                        if (median1 == median2)
                        {
                            int median = maxA1>arrayB[minB_index] ? maxA1 : arrayB[minB_index];
                            median = median<minA2 ? median : minA2;
                            return median;
                        }
                        else
                        {
                            int median = maxA1>arrayB[minB_index] ? maxA1 : arrayB[minB_index];
                            return (median+minA2)/2.0;
                        }
                    }
                }

                if (maxA1 < maxB1)
                {
                    if (minA2 < minB2)
                    {
                        minA_index += sizeA-divide_num;
                        sizeA = divide_num;
                        sizeB = divide_num;
                    }
                    else if (median1 == median2)
                    {
                        return maxB1;
                    }
                    else
                    {
                        return (maxB1+minB2)/2.0;
                    }
                }
                else
                {
                    if (minA2 > minB2)
                    {
                        minB_index += divide_num;
                        sizeB -= divide_num;
                        sizeA -= divide_num;
                    }
                    else if (median1 == median2)
                    {
                        return maxA1;
                    }
                    else
                    {
                        return (maxA1+minA2)/2.0;
                    }
                }
            }
            return -1;
        }
    };
{% endhighlight %}
