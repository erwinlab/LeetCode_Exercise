# LeetCode_Exercise
1. Bitmask

Concluded some bit manipulation and bitmask exercises, difficulty goes from low to high
总结几道非常有意义的位运算和Bitmask题，难度由低到高


https://leetcode.com/problems/number-of-1-bits/ (easy)

https://leetcode.com/problems/single-number/ (easy)

https://leetcode.com/problems/sum-of-two-integers/ (highly recommend)

https://leetcode.com/problems/bitwise-and-of-numbers-range/submissions/ (medium)

https://leetcode.com/problems/bitwise-ors-of-subarrays/ (medium) (highly recommend)

https://leetcode.com/problems/single-number-ii/ (medium) (highly recommend, even u can think how to solve the problem when every elements appear 4,5,6 times and only one appers 1 time, how can u solve it)

https://leetcode.com/problems/single-number-iii/ (medium) (highly recommend)

https://leetcode.com/problems/number-of-ways-to-wear-different-hats-to-each-other/ (hard) (highly recommend)


Why we need to use Bitmask to handle some difficult problems?
Reason 1: bit manipulation is faster than normal operation such as '+-*/'
Reason 2: bitmask can help us record the state. if you use an int variable or boolean variable to store the the state, it will cost 4 bytes, so you use bit 0/1 to store the state, that will save lots of space.

Some bitmask's operation:
1.Record state
you can initiate the bitmask as 00000000 if you have 8 states, and use 'or |' operation to save your state.
eg.
bitmask = 00000000
new_state1 = 00001000
bitmask | new_state1 = 00001000 which means the fourth state have been recorded
new_state2 = 00010001
bitmask | new_state2 = 00011001 which means the fifth and first state have been recorded

2. query state
you can use 0..1..0 and 'and &' operation to query the states, the position of 1 means which state you want to query.
eg.
bitmask = 01011111
query_state1 = 00001000
bitmask & query_state1 = 00001000 means the fourth state was recorded
query_state2 = 10000000
bitmask & query_state2 = 00000000 means the eighth state was not recorded

3. delete state
you can use 1..0..1 and 'and &' operation to query the states, the position of 1 means which state you want to query.
eg.
bitmask = 10100110
delete_state1 = 11011111
bitmask & delete_state1 = 10000110 means the sixth state was deleted
delete_state2 = 11111011
bitmask & delete_state2 = 10000011 means the third state was deleted

4. set,keep and delete state
you can use 'XOR ^' operation to set,keep and delete the states
eg.
bitmask = 10101111
transfer_state = 00010110
bitmask ^ transfer_state = 10111001

I will show how transfer state work and explain it in the later of the article.


Later I will show some code for the bitmask excersices and explain why these works and helped a lot.

1.
Leetcode single number (explain how transfer state works)
https://leetcode.com/problems/single-number/
code:
  def singleNumber(self, nums: List[int]) -> int:
        bitmask = 0                  #initial state
        for i in nums:               
            bitmask^=i               
        return bitmask
          
Explanation:
In this question, we know every elements will appear twice and only one element appear once, which means every bit will appear 2*n times
and only the one elements' bit will appear 2*n+1 times. So we need to set and keep the state when the bit appear 2*n+1 times and delete
state when bit appear 2*n times.

2.
Leetcode single number 2
https://leetcode.com/problems/single-number-ii/
code:
  def singleNumber(self, nums: List[int]) -> int:
        pre_bitone = 0   #store every bit which appears once(3*n+1)
        pre_bittwo = 0   #store every bit which appears twice(3*n+2,i called it once and twice because if bits appear 3 times it will be deleted)
        for i in nums:
            cur_bittwo = pre_bittwo^(pre_bittwo&i)|(pre_bitone&i)
            cur_bitone = pre_bitone^(~pre_bittwo&i)
            pre_bitone = cur_bitone
            pre_bittwo = cur_bittwo
        return pre_bitone

Explanation:
cur_bittwo means when we retrieve this number(i), which bits appears twice(3*n), so we need to consider about this: 
1.those bits who already appears twice and appear in this number(i) should be deleted, because this is its third appearance, so we need use (pre_bittwo&i) to find which bits appear 3 times, and use pre_bittwo^(pre_bittwo&i) to keep those bits appears twice and delete the bits appears 3 times. 

2.we have some bits appears once and also appears in this number(i), so it appears twice after this number, we need to find them. so we use (pre_bitone&i) to find all bits appears once and also appears in this number(i). 

3.we use 'or |' to combine those bits in step 1 and step 2 to find all bits appears twice.

cur_bitone means when we retrieve this number(i), which bits appear once(3*n+1), so we need to consider about this:
1. we only need to find all bits which appears 0(3*n) times, also find those bits which appears once(3*n+1) and dont appear in this number. 

2. those bits who appears twice before this number(i) should be removed from our thinking, because it only can appears twice or 0 times
(3*n+2 times and 3*n times). So we use pre_bittwo to find all these bits, then use (~pre_bittwo&i) to remove these bits from i.

3. like we do in cur_bittwo, using pre_bitone^(~pre_bittwo&i) to find all bits appears once.

Finally, the pre_bitone is the answer(appears once)
