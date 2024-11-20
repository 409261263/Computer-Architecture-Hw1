# Assignment1:  RISC-V Assembly and Instruction Pipeline
contributed by < [`陳奕嘉`]() >

## Quize 1 Problem C
```c
#include <stdio.h>
#include <stdint.h>

static inline int my_clz(uint32_t x) {
    int count = 0;
    for (int i = 31; i >= 0; --i) {
        if (x & (1U << i))
            break;
        count++;
    }
    return count;
}

int main() {
    
    uint32_t test_value1 = 0x00000001;
    int result1 = my_clz(test_value1);
    if (result1 == 31) {
        printf("Test 1: SUCCESS\n");
    } else {
        printf("Test 1: FAIL (Expected 31, Got %d)\n", result1);
    }

    
    uint32_t test_value2 = 0x80000000;
    int result2 = my_clz(test_value2);
    if (result2 == 0) {
        printf("Test 2: SUCCESS\n");
    } else {
        printf("Test 2: FAIL (Expected 0, Got %d)\n", result2);
    }

    
    uint32_t test_value3 = 0x0F000000;
    int result3 = my_clz(test_value3);
    if (result3 == 4) {
        printf("Test 3: SUCCESS\n");
    } else {
        printf("Test 3: FAIL (Expected 4, Got %d)\n", result3);
    }

    return 0;
}
```


* **Counter Variable `count`**:
    * The `count` variable keeps track of the number of leading zeros (zeros that appear before the first `1`).
    * It is initialized to `0` and is incremented each time a `0` is encountered in the loop.


* **`x & (1U << i)`**:
    * This is a bitwise operation used to check if the `i`th bit of `x` is `1`. The expression `1U << i` shifts the number `1` left by `i` positions, creating a number where only the `ith` bit is set to `1`.
    * If the result of `x & (1U << i)` is non-zero, it means that the `i`th bit of `x` is `1`, and the function should stop counting zeros at that point.

### Summary

* `my_clz` is a simple bit manipulation function that calculates the number of leading zeros in a given number's binary representation.
* The function checks each bit, starting from the most significant bit, and stops once it finds the first `1`, returning the number of zeros encountered before it.


:::danger
Don't paste code snip without comprehensive discussions.
:::

## Problem C (Risc-v assembly code)
```c
.data
test_value1: 
    .word 0x00000001         # Test data 1: only the least significant bit is 1
test_value2:
    .word 0x80000000         # Test data 2: the most significant bit is 1, expected 0 leading zeroes
test_value3:
    .word 0x0F000000         # Test data 3: first four bits are 0 values 

success_msg:
    .string "SUCCESS\n"       # Success message
fail_msg1:
    .string "FAIL (Expected 31, Got "  # Fail message part 1
fail_msg2:
    .string "FAIL (Expected 0, Got "   # Fail message part 2
fail_msg3:
    .string "FAIL (Expected 4, Got "   # Fail message part 3
fail_end:
    .string ")\n"                       # End of fail message
test1_msg:
    .string "Test 1: "
test2_msg:
    .string "Test 2: "
test3_msg:
    .string "Test 3: "

.text
.globl main

# Main function
main:
    addi sp, sp, -32          # Create stack frame
    sw ra, 28(sp)             # Save return address
    sw s0, 24(sp)             # Save s0
    addi s0, sp, 32           # Set frame pointer

    # Test 1
    la a0, test1_msg          # Load "Test 1: " message
    li a7, 4                  # Syscall for printing string
    ecall
    lw a0, test_value1        # Load test_value1
    jal ra, my_clz            # Call my_clz function
    li t1, 31                 # Expected result for test 1 is 31
    beq a0, t1, test1_success # If result matches, jump to success
    # Fail case
    la a0, fail_msg1          # Load failure message part 1
    li a7, 4                  # Syscall for printing string
    ecall
    mv a1, a0                 # Save actual result to a1
    li a7, 1                  # Syscall for printing integer
    ecall
    la a0, fail_end           # Load end of fail message
    li a7, 4                  # Syscall for printing string
    ecall
    j continue_test1          # Continue to next test
test1_success:
    la a0, success_msg        # Load success message
    li a7, 4                  # Syscall for printing string
    ecall

continue_test1:
    # Test 2
    la a0, test2_msg          # Load "Test 2: " message
    li a7, 4                  # Syscall for printing string
    ecall
    lw a0, test_value2        # Load test_value2
    jal ra, my_clz            # Call my_clz function
    li t1, 0                  # Expected result for test 2 is 0
    beq a0, t1, test2_success # If result matches, jump to success
    # Fail case
    la a0, fail_msg2          # Load failure message part 2
    li a7, 4                  # Syscall for printing string
    ecall
    mv a1, a0                 # Save actual result to a1
    li a7, 1                  # Syscall for printing integer
    ecall
    la a0, fail_end           # Load end of fail message
    li a7, 4                  # Syscall for printing string
    ecall
    j continue_test2          # Continue to next test
test2_success:
    la a0, success_msg        # Load success message
    li a7, 4                  # Syscall for printing string
    ecall

continue_test2:
    # Test 3
    la a0, test3_msg          # Load "Test 3: " message
    li a7, 4                  # Syscall for printing string
    ecall
    lw a0, test_value3        # Load test_value3
    jal ra, my_clz            # Call my_clz function
    li t1, 4                  # Expected result for test 3 is 4
    beq a0, t1, test3_success # If result matches, jump to success
    # Fail case
    la a0, fail_msg3          # Load failure message part 3
    li a7, 4                  # Syscall for printing string
    ecall
    mv a1, a0                 # Save actual result to a1
    li a7, 1                  # Syscall for printing integer
    ecall
    la a0, fail_end           # Load end of fail message
    li a7, 4                  # Syscall for printing string
    ecall
    j end_program             # Continue to end
test3_success:
    la a0, success_msg        # Load success message
    li a7, 4                  # Syscall for printing string
    ecall

end_program:
    li a7, 10                 # Syscall for exit
    ecall

# my_clz function: Calculate the number of leading zeros
my_clz:
    li t0, 0                  # Initialize count = 0 (start from the lowest bit)
    li t1, 32                 # Maximum bit count
    mv t2, a0                 # Store the original a0 value
clz_loop:
    srli t3, t2, 31            # Shift right by 31 to check the highest bit
    beqz t3, not_found        # If bit is 0, increment count
    mv a0, t0                 # Return the count if found
    ret
not_found:
    addi t0, t0, 1            # Increment the zero count
    slli t2, t2, 1             # Shift the value to the left
    addi t1, t1, -1           # Decrement bit count
    bnez t1, clz_loop         # Continue looping if t1 is not zero
    li a0, 32                 # Return 32 if no leading one is found
    ret
```

:::danger
Use fewer instructions.
:::

## Problem C(Risc-v assembly code with loop unrolling)

```c
.data
test_value1: 
    .word 0x00000001         # Test data 1: only the least significant bit is 1
test_value2:
    .word 0x80000000         # Test data 2: the most significant bit is 1, expected 0 leading zeroes
test_value3:
    .word 0x0F000000         # Test data 3: first four bits are 0 values 

success_msg:
    .string "SUCCESS\n"       # Success message
fail_msg1:
    .string "FAIL (Expected 31, Got "  # Fail message part 1
fail_msg2:
    .string "FAIL (Expected 0, Got "   # Fail message part 2
fail_msg3:
    .string "FAIL (Expected 4, Got "   # Fail message part 3
fail_end:
    .string ")\n"                       # End of fail message
test1_msg:
    .string "Test 1: "
test2_msg:
    .string "Test 2: "
test3_msg:
    .string "Test 3: "

.text
.globl main

# Main function
main:
    addi sp, sp, -32          # Create stack frame
    sw ra, 28(sp)             # Save return address
    sw s0, 24(sp)             # Save s0
    addi s0, sp, 32           # Set frame pointer

    # Test 1
    la a0, test1_msg          # Load "Test 1: " message
    li a7, 4                  # Syscall for printing string
    ecall
    lw a0, test_value1        # Load test_value1
    jal ra, my_clz            # Call my_clz function
    li t1, 31                 # Expected result for test 1 is 31
    beq a0, t1, test1_success # If result matches, jump to success
    # Fail case
    la a0, fail_msg1          # Load failure message part 1
    li a7, 4                  # Syscall for printing string
    ecall
    mv a1, a0                 # Save actual result to a1
    li a7, 1                  # Syscall for printing integer
    ecall
    la a0, fail_end           # Load end of fail message
    li a7, 4                  # Syscall for printing string
    ecall
    j continue_test1          # Continue to next test
test1_success:
    la a0, success_msg        # Load success message
    li a7, 4                  # Syscall for printing string
    ecall

continue_test1:
    # Test 2
    la a0, test2_msg          # Load "Test 2: " message
    li a7, 4                  # Syscall for printing string
    ecall
    lw a0, test_value2        # Load test_value2
    jal ra, my_clz            # Call my_clz function
    li t1, 0                  # Expected result for test 2 is 0
    beq a0, t1, test2_success # If result matches, jump to success
    # Fail case
    la a0, fail_msg2          # Load failure message part 2
    li a7, 4                  # Syscall for printing string
    ecall
    mv a1, a0                 # Save actual result to a1
    li a7, 1                  # Syscall for printing integer
    ecall
    la a0, fail_end           # Load end of fail message
    li a7, 4                  # Syscall for printing string
    ecall
    j continue_test2          # Continue to next test
test2_success:
    la a0, success_msg        # Load success message
    li a7, 4                  # Syscall for printing string
    ecall

continue_test2:
    # Test 3
    la a0, test3_msg          # Load "Test 3: " message
    li a7, 4                  # Syscall for printing string
    ecall
    lw a0, test_value3        # Load test_value3
    jal ra, my_clz            # Call my_clz function
    li t1, 4                  # Expected result for test 3 is 4
    beq a0, t1, test3_success # If result matches, jump to success
    # Fail case
    la a0, fail_msg3          # Load failure message part 3
    li a7, 4                  # Syscall for printing string
    ecall
    mv a1, a0                 # Save actual result to a1
    li a7, 1                  # Syscall for printing integer
    ecall
    la a0, fail_end           # Load end of fail message
    li a7, 4                  # Syscall for printing string
    ecall
    j end_program             # Continue to end
test3_success:
    la a0, success_msg        # Load success message
    li a7, 4                  # Syscall for printing string
    ecall

end_program:
    li a7, 10                 # Syscall for exit
    ecall

# my_clz function: Calculate the number of leading zeros with loop unrolling
my_clz:
    li t0, 0                  # Initialize count = 0 (start from the lowest bit)
    li t1, 32                 # Maximum bit count
    mv t2, a0                 # Store the original a0 value

    # Loop unrolling: process 4 bits at a time
unroll_loop:
    srli t3, t2, 28            # Shift right by 28 bits (4 bits at a time)
    beqz t3, not_found_4bit    # If all 4 bits are zero, increase count by 4
    j check_each_bit           # If not zero, jump to bit-by-bit checking
not_found_4bit:
    addi t0, t0, 4             # Increment count by 4
    slli t2, t2, 4             # Shift the value left by 4 bits
    addi t1, t1, -4            # Decrease bit count by 4
    bnez t1, unroll_loop       # Continue unrolling if t1 is not zero

check_each_bit:
    # Now check each remaining bit individually
clz_loop:
    srli t3, t2, 31            # Shift right by 31 bits to check the highest bit
    beqz t3, not_found         # If bit is 0, increment count
    mv a0, t0                  # Return the count if found
    ret
not_found:
    addi t0, t0, 1             # Increment the zero count
    slli t2, t2, 1             # Shift the value to the left
    addi t1, t1, -1            # Decrement bit count
    bnez t1, clz_loop          # Continue looping if t1 is not zero
    li a0, 32                  # Return 32 if no leading one is found
    ret

```

## Performance 





|  | Normal version| Loop unrolling version |
| -------- | -------- | -------- |
| Cycles     | 452    |  227   |
| Instrs. retired     |276    | 141   |
| CPI     |  1.64    |   1.61   |
| IPC     | 0.611    |  0.621   |
| Clock rate     | 130.18Hz     |144.31Hz    |

### Analysis with loop unrolling
In the `my_clz` function, `4-bit` loop unrolling is used, meaning it processes `4` bits at a time:

**1.** `srli t3, t2, 28`

* This instruction shifts the number in `t2` to the right by `28` bits, leaving the highest `4` bits (starting from the leftmost side of the `32-bit` integer).
* This means that it checks whether the `4` highest bits are all zeros. If all `4` bits are zeros, we can skip checking each bit individually and directly increase the zero counter by `4`.

**2.** `beqz t3, not_found_4bit`

* This instruction checks the value in `t3`. If shifting by `28` bits results in `t3` being `0`, it means the highest `4` bits are all zeros, and the program jumps to the `not_found_4bit` section to handle this case by increasing the leading zero count by `4`.

**3.** `addi t0, t0, 4`

* If the `4` highest bits are found to be zeros, the leading zero counter `t0` is incremented by `4` because those `4` bits are zeros.

**4.** `slli t2, t2, 4`

* This shifts the number left by `4` bits, removing the highest `4` bits that were just checked. This way, the next iteration will check the next `4` bits.

**5.** `addi t1, t1, -4`

* This instruction decreases the remaining bit count by `4`, as we process `4` bits at a time in the loop unrolling. Initially, `t1` holds the total bit count (`32` for a `32-bit` integer), and after each `4-bit` check, we subtract `4` from `t1`. When `t1` becomes `0`, it indicates that all bits have been checked.

**6.** `bnez t1, unroll_loop`



* This checks the remaining bit count. If there are still bits to be checked , it continues with the next `4-bit` check.

**7.** `j check_each_bit`

*  If any non-zero bits are found during the `4-bit` check, it jumps to the section for checking individual bits, which is `check_each_bit`.









## LeetCode 342 (Power of Four)
The question I choose is [342. Power of Four ](https://leetcode.com/problems/power-of-four/description/),it is an `easy` question.


>Given an integer n, return true if it is a power of four. Otherwise, return false.
>
>An integer n is a power of four, if there exists an integer x such that n >== 4x.
>
> 
>
>Example 1:
>
>Input: n = 16
>Output: true
>
>Example 2:
>
>Input: n = 5
>Output: false
>
>Example 3:
>
>Input: n = 1
>Output: true
>
>Constraints:
>-231 <= n <= 231 - 1
>
>
>Follow up: Could you solve it without loops/recursion?
>
>


## Solution
### Thinking path
Using `__builtin_clz` learned in `problem C`, utilize the similar function `calculate_ctz` to first calculate how many trailing zeros there are. Then, determine whether the number of trailing zeros is even and if the input test value is a power of `2`.
## C code

```c
#include <stdio.h>
#include <stdbool.h>

// Manually implement __builtin_ctz function to calculate the number of trailing zeros
int calculate_ctz(int n) {
    int count = 0;
    while ((n & 1) == 0) {  // As long as the least significant bit is 0, count and right shift
        count++;
        n >>= 1;
    }
    return count;  // Return the number of trailing zeros
}

// Function to check if the number is a power of four
bool isPowerOfFour(int n) {
    if (n < 1) return false;

    int tzs = calculate_ctz(n);  // Calculate the trailing zeros

    if (tzs & 1) return false;   // Check if the number of trailing zeros is odd

    return n == (1 << tzs);  // Check if n is equal to 1 << tzs
}

int main() {
    int test1 = 1073741824;
    int test2 = 268435456;
    int test3 = 536870912;

    // Check test1
    if (isPowerOfFour(test1)) {
        printf("%d is power of four\n", test1);
    } else {
        printf("%d is not power of four\n", test1);
    }

    // Check test2
    if (isPowerOfFour(test2)) {
        printf("%d is power of four\n", test2);
    } else {
        printf("%d is not power of four\n", test2);
    }

    // Check test3
    if (isPowerOfFour(test3)) {
        printf("%d is power of four\n", test3);
    } else {
        printf("%d is not power of four\n", test3);
    }

    return 0;
}


```




## Program explaination
<br>

```c
const int tzs = calculate_ctz(n);
```

Use `calculate_ctz` to calculate how many trailing zeros there are in the input value before encountering the first `1`. If a number is a power of `4`, the zeros after the only `1` must be an even number of zeros (because `4` is the square of `2`, and each time the power of `4` increases, the power of `2` also increases by `2`. Therefore, the binary number is left-shifted by `2`, which means an even number of zeros is added). 

#### Example
- $4 = 2^2$ : Shift `1` to the left by `2` positions, resulting in `100`.

- $16 = 2^4$: Shift `1` to the left by `4` positions, resulting in `10000` 

- $64 = 2^6$: Shift `1` to the left by `6` positions, resulting in `1000000` 

#### Conclusion
The power of `4` can be represented as:
$$
4^k=2^{2k}
$$

Thus, it is a power of $2^{2k}$, which means shifting left by $2k$ positions 
(where $k$ is a non-negative integer).

```c
if (tzs & 1) return false;
```
Use the binary representation of `tzs` and `1` to compare whether `tzs` is an even or odd number.

#### Example
`tzs=2(0010)` 
`1(0001)`
`0010 & 0001 = 0000`
At this point, it returns `0`, indicating that `tzs` is an even number.


```c
return n == (1 << tzs);
```
Shift `1` left by `tzs` positions and then check if it equals `n` to confirm whether it is a power of 2 (because $4^k = 2^{2k}$).

## RISC-V assembly code

```c
.data
test1: 
    .word 1073741824                   # Test data 1
test2:
    .word 268435456                  # Test data 2
test3:
    .word 536870912                  # Test data 3

LC0: 
    .string " is power of four\n"  # String for correct result output
LC1:
    .string " is not power of four\n" # String for incorrect result output

.text
.globl main
main:
    addi sp, sp, -32          # Create stack frame
    sw ra, 28(sp)             # Save return address
    sw s0, 24(sp)             # Save s0
    addi s0, sp, 32           # Set frame pointer

    # Check test1
    lw a0, test1              # Load test1 data
    jal ra, isPowerOfFour     # Call isPowerOfFour function
    beqz a0, not_power_of_four1 # If the return value is 0, jump to "not a power of four"

    # If it is a power of four, display the test number
    lw a0, test1              # Load the test number into a0
    li a7, 1                  # System call number 1 (print integer)
    ecall                     # Print integer
    la a0, LC0                # Load string "is power of four"
    li a7, 4                  # System call number 4 (print string)
    ecall                     # Print string
    j continue_execution1

not_power_of_four1:
    # If it is not a power of four, display the test number
    lw a0, test1              # Load the test number into a0
    li a7, 1                  # System call number 1 (print integer)
    ecall                     # Print integer
    la a0, LC1                # Load string "is not power of four"
    li a7, 4                  # System call number 4 (print string)
    ecall                     # Print string

continue_execution1:
    # Check test2
    lw a0, test2              # Load test2 data
    jal ra, isPowerOfFour     # Call isPowerOfFour function
    beqz a0, not_power_of_four2 # If the return value is 0, jump to "not a power of four"

    # If it is a power of four, display the test number
    lw a0, test2              # Load the test number into a0
    li a7, 1                  # System call number 1 (print integer)
    ecall                     # Print integer
    la a0, LC0                # Load string "is power of four"
    li a7, 4                  # System call number 4 (print string)
    ecall                     # Print string
    j continue_execution2

not_power_of_four2:
    # If it is not a power of four, display the test number
    lw a0, test2              # Load the test number into a0
    li a7, 1                  # System call number 1 (print integer)
    ecall                     # Print integer
    la a0, LC1                # Load string "is not power of four"
    li a7, 4                  # System call number 4 (print string)
    ecall                     # Print string

continue_execution2:
    # Check test3
    lw a0, test3              # Load test3 data
    jal ra, isPowerOfFour     # Call isPowerOfFour function
    beqz a0, not_power_of_four3 # If the return value is 0, jump to "not a power of four"

    # If it is a power of four, display the test number
    lw a0, test3              # Load the test number into a0
    li a7, 1                  # System call number 1 (print integer)
    ecall                     # Print integer
    la a0, LC0                # Load string "is power of four"
    li a7, 4                  # System call number 4 (print string)
    ecall                     # Print string
    j end_execution

not_power_of_four3:
    # If it is not a power of four, display the test number
    lw a0, test3              # Load the test number into a0
    li a7, 1                  # System call number 1 (print integer)
    ecall                     # Print integer
    la a0, LC1                # Load string "is not power of four"
    li a7, 4                  # System call number 4 (print string)
    ecall                     # Print string

end_execution:
    # End the program
    li a7, 10                 # System call number 10 (exit)
    ecall                     # Call system to exit

# Function: isPowerOfFour
isPowerOfFour:
    addi sp, sp, -16          # Create stack frame
    sw ra, 12(sp)             # Save return address
    sw s0, 8(sp)              # Save s0
    addi s0, sp, 16           # Set frame pointer

    bgtz a0, check_trailing_zeroes # Check if n > 0
    li a0, 0                  # Return false
    j end_function

check_trailing_zeroes:
    mv t1, a0                 # Save the original value of n
    jal ra, calculate_ctz      # Manually calculate trailing zeros
    mv t2, a0                 # Store the value of tzs in t2

    andi t3, t2, 1            # Check if tzs is odd
    bnez t3, return_false     # If it is odd, return false

    li t3, 1
    sll t3, t3, t2            # Calculate 1 << tzs
    beq t1, t3, return_true   # If n == (1 << tzs), return true

return_false:
    li a0, 0                  # Return false
    j end_function

return_true:
    li a0, 1                  # Return true

end_function:
    lw ra, 12(sp)             # Restore return address
    lw s0, 8(sp)              # Restore s0
    addi sp, sp, 16           # Restore stack frame
    jr ra                     # Return

# Manually implemented function to calculate trailing zeros
calculate_ctz:
    li t2, 0                  # Initialize t2 = 0 for counting trailing zeros
calc_loop:
    andi t3, a0, 1            # Check the least significant bit
    bnez t3, calc_done        # If the least significant bit is 1, end
    srli a0, a0, 1            # Right shift by one bit
    addi t2, t2, 1            # Increment trailing zero counter
    j calc_loop               # Continue the loop
calc_done:
    mv a0, t2                 # Return the number of trailing zeros
    ret
```

:::danger
Use fewer instructions.
:::

## RISC-V assembly code with loop unrolling

```c
.data
test1: 
    .word 1073741824                  # Test data 1
test2:
    .word 268435456                  # Test data 2
test3:
    .word 536870912                  # Test data 3

LC0: 
    .string " is power of four\n"  # String for correct result output
LC1:
    .string " is not power of four\n" # String for incorrect result output

.text
.globl main
main:
    addi sp, sp, -32          # Create stack frame
    sw ra, 28(sp)             # Save return address
    sw s0, 24(sp)             # Save s0
    addi s0, sp, 32           # Set frame pointer

    # Check test1
    lw a0, test1              # Load test1 data
    jal ra, isPowerOfFour     # Call isPowerOfFour function
    beqz a0, not_power_of_four1 # If the return value is 0, jump to "not a power of four"

    # If it is a power of four, display the test number
    lw a0, test1              # Load the test number into a0
    li a7, 1                  # System call number 1 (print integer)
    ecall                     # Print integer
    la a0, LC0                # Load string "is power of four"
    li a7, 4                  # System call number 4 (print string)
    ecall                     # Print string
    j continue_execution1

not_power_of_four1:
    # If it is not a power of four, display the test number
    lw a0, test1              # Load the test number into a0
    li a7, 1                  # System call number 1 (print integer)
    ecall                     # Print integer
    la a0, LC1                # Load string "is not power of four"
    li a7, 4                  # System call number 4 (print string)
    ecall                     # Print string

continue_execution1:
    # Check test2
    lw a0, test2              # Load test2 data
    jal ra, isPowerOfFour     # Call isPowerOfFour function
    beqz a0, not_power_of_four2 # If the return value is 0, jump to "not a power of four"

    # If it is a power of four, display the test number
    lw a0, test2              # Load the test number into a0
    li a7, 1                  # System call number 1 (print integer)
    ecall                     # Print integer
    la a0, LC0                # Load string "is power of four"
    li a7, 4                  # System call number 4 (print string)
    ecall                     # Print string
    j continue_execution2

not_power_of_four2:
    # If it is not a power of four, display the test number
    lw a0, test2              # Load the test number into a0
    li a7, 1                  # System call number 1 (print integer)
    ecall                     # Print integer
    la a0, LC1                # Load string "is not power of four"
    li a7, 4                  # System call number 4 (print string)
    ecall                     # Print string

continue_execution2:
    # Check test3
    lw a0, test3              # Load test3 data
    jal ra, isPowerOfFour     # Call isPowerOfFour function
    beqz a0, not_power_of_four3 # If the return value is 0, jump to "not a power of four"

    # If it is a power of four, display the test number
    lw a0, test3              # Load the test number into a0
    li a7, 1                  # System call number 1 (print integer)
    ecall                     # Print integer
    la a0, LC0                # Load string "is power of four"
    li a7, 4                  # System call number 4 (print string)
    ecall                     # Print string
    j end_execution

not_power_of_four3:
    # If it is not a power of four, display the test number
    lw a0, test3              # Load the test number into a0
    li a7, 1                  # System call number 1 (print integer)
    ecall                     # Print integer
    la a0, LC1                # Load string "is not power of four"
    li a7, 4                  # System call number 4 (print string)
    ecall                     # Print string

end_execution:
    # End the program
    li a7, 10                 # System call number 10 (exit)
    ecall                     # Call system to exit

# Function: isPowerOfFour
isPowerOfFour:
    addi sp, sp, -16          # Create stack frame
    sw ra, 12(sp)             # Save return address
    sw s0, 8(sp)              # Save s0
    addi s0, sp, 16           # Set frame pointer

    bgtz a0, check_trailing_zeroes # Check if n > 0
    li a0, 0                  # Return false
    j end_function

check_trailing_zeroes:
    mv t1, a0                 # Save the original value of n
    jal ra, calculate_ctz      # Manually calculate trailing zeros
    mv t2, a0                 # Store the value of tzs in t2

    andi t3, t2, 1            # Check if tzs is odd
    bnez t3, return_false     # If it is odd, return false

    li t3, 1
    sll t3, t3, t2            # Calculate 1 << tzs
    beq t1, t3, return_true   # If n == (1 << tzs), return true

return_false:
    li a0, 0                  # Return false
    j end_function

return_true:
    li a0, 1                  # Return true

end_function:
    lw ra, 12(sp)             # Restore return address
    lw s0, 8(sp)              # Restore s0
    addi sp, sp, 16           # Restore stack frame
    jr ra                     # Return

# More optimized 4-bit loop unrolling in calculate_ctz
calculate_ctz:
    li t2, 0                  # Initialize t2 = 0 for counting trailing zeros
    andi t3, a0, 1            # Check the least significant bit
    bnez t3, calc_done        # If the least significant bit is 1, end
    li t4, 32                 # Initialize bit count to 32

    # Loop unrolling: process 4 bits at a time
unroll_loop:
    andi t3, a0, 0xF          # Check the next 4 least significant bits
    beqz t3, not_found_4bit      # If 4 bits are zero, continue unrolling
    j check_each_bit
not_found_4bit:
    addi t2, t2, 4            # Increment trailing zero count by 4
    srli a0, a0, 4            # Right shift 4 bits
    addi t4, t4, -4           # Decrease bit count by 4
    bnez t4, unroll_loop      # Continue unrolling if bits remain

check_each_bit:
    # Now check each remaining bit individually
calc_loop:
    andi t3, a0, 1            # Check the least significant bit
    bnez t3, calc_done        # If the least significant bit is 1, end
    j not_found
    
not_found:
    addi t2,t2,1
    srli a0,a0,1
    addi t4,t4,-1
    bnez t4,calc_loop
    
calc_done:
    mv a0, t2                 # Return the number of trailing zeros
    ret
```

## Performance 


:::danger
Do not use screenshots for plain text content, as this is inaccessible to visually impaired users.
:::


|  | Normal version| Loop unrolling version |
| -------- | -------- | -------- |
| Cycles     |  788    | 446     |
| Instrs. retired     |  546 | 276    |
| CPI     |    1.44  |  1.62    |
| IPC     |   0.693   |  0.619    |
| Clock rate     | 814.89Hz     | 816.85Hz     |

### Analysis with loop unrolling

**1. Initialization**

* `li t2, 0`: Initializes the trailing zero counter `t2` to 0, which will hold the final count of trailing zeros.
* `andi t3, a0, 1`: Checks the least significant bit of the input number `a0` by ANDing it with `1`. If the least significant bit is `1`, it indicates that there are no trailing zeros, so the program jumps to `calc_done` to return the count.

**2. Starting Loop Unrolling**

* `andi t3, a0, 0xF`: This instruction checks if the lowest `4` bits of `a0` are zeros by ANDing `a0` with `0xF` (which is `1111` in binary). This checks if all `4` bits are zero.
* `beqz t3, not_found_4bit`: If all `4` bits are zero (result is `0`), it jumps to `not_found_4bit` to handle this case, otherwise it jumps to `check_each_bit` to perform a bit-by-bit check.

**3. Handling 4 Zeros**

 * `not_found_4bit`: When a group of `4` trailing zeros is found:
    * `addi t2, t2, 4`: The trailing zero counter `t2` is incremented by `4` because we found `4` zero bits.
    * `srli a0, a0, 4`: The number is shifted right by `4` bits to continue checking the next `4` bits.
    * `addi t4, t4, -4`: The bit count `t4` is decremented by `4`.
    
    * `bnez t4, unroll_loop`: If there are still bits remaining, the loop continues to check the next `4` bits.
    
**4. Checking Remaining Bits Individually**
 When the `4-bit` check fails (some bits are not zero), it jumps to `check_each_bit`, where it checks the remaining bits one by one:

* `andi t3, a0, 1`: Checks if the least significant bit is `1`.
* `bnez t3, calc_done`: If  `1` is found, it jumps to `calc_done` to finish counting and return the result.
* `addi t2, t2, 1`: If the current bit is `0`, the trailing zero counter `t2` is incremented by `1`.
* `srli a0, a0, 1`: The number is shifted right by `1` bit to check the next bit.
* `bnez t4, calc_loop`: Continues checking until all bits have been processed.
<br>



## 5-stage pipelined processor

### Ripes
The above Assembly code was successfully executed on Ripes, and the `5-stage pipelined processor` can be divided into the following five stages:

| Stage | Description                        |
| ----- | ---------------------------------- |
| IF    | Instruction Fetch                  |
| ID    | Instruction Decode & Register Read |
| EX    | Execution or Address Calculation   |
| MEM   | Data Memory Access                 |
| WB    | Write Back                         |

**Stage 1: Instruction Fetch**

* CPU reads the instruction from memory using the address in the program counter (PC).

**Stage 2: Instruction Decode**

* Instruction is decoded, and register values are accessed. The CPU determines whether to use `Reg1 + Reg2` or `Reg1 + Imm` based on the instruction.

**Stage 3: Instruction Execute**

* ALU performs the required operation.

**Stage 4: Memory Access**
* Memory is read or written as specified by the instruction.

**Stage 5: Write Back**
* The result is written back to the destination register.



### Example

**`Addi x8 x2 32`**

This is an `I-type` instruction in `RISC-V`, performing an immediate addition, where the value of register `x2` is added to the immediate value `32`, and the result is stored in register `x8`.

 **I-Type Instruction Format:**
`| imm[11:0]  | rs1[4:0]  | funct3[2:0] | rd[4:0]  | opcode[6:0] |` 
 * **Opcode :** `7` bits, the opcode for addi is `(0010011)`
 * **rd :** `5` bits, the destination register `x8(01000)`
 * **funct3 :** `3` bits, the function code for addi is `(000)`
 * **rs1 :** `5` bits, source register `x2(00010)`
 * **imm :** `12` bits immediate value `32(000000100000)`






`Addi x8 x2 32 : 0000 0010 0000 0001 0000 0100 0001 0011 =  0x02010413`

### Execution process in the 5-stage pipeline:

`NOTE:`
`x2 = 0x7FFFFFD0`
`x8 = 0x00000000`

**1. Instruction Fetch (IF)**


![螢幕擷取畫面 2024-10-14 145953](https://hackmd.io/_uploads/r16JwP9kkg.png)

* **Action :** 
The instruction is fetched from memory using the address from the program counter (PC).The CPU fetches the addi instruction from the memory based on the PC value and loads it into the instruction register. As shown in the `Instr. memory`, the instruction displayed is `0x02010413`, which corresponds to a RISC-V instruction `Addi x8, x2, 32`.


**2. Instruction Decode (ID)**

![螢幕擷取畫面 2024-10-14 180247](https://hackmd.io/_uploads/BknyTv9yke.png)

  * **Action :**
     The instruction is decoded, the CPU reads the register file, and the immediate value is sign-extended.The immediate value `32` is sign-extended from `12` bits to `32` bits :    
     `0000 0000 0000 0000 0000 0000 0010 0000`.In this stage, the CPU reads the value of `x2` from the register file to be used as one input to the ALU. As observed in the above diagram, the Imm signal is `0x00000020`, which is the `32-bit` extended value of `32`. Meanwhile, the signal in `Reg 1` is `0x7FFFFFD0`. However, it is important to note that the value in the memory is still `0x7FFFFFF0` and has not yet been updated to `0x7FFFFFD0`. This is because the previous instruction `addi x2, x2, -32` has not yet written the correct value into `x2`. However, this does not result in a data hazard, because this instruction has not yet entered the EX stage where the ALU would be used to execute the addition.

**3. Instruction Execute (EX)**
![螢幕擷取畫面 2024-10-14 204047](https://hackmd.io/_uploads/BkGvWq51Je.png)![螢幕擷取畫面 2024-10-14 204147](https://hackmd.io/_uploads/H1j5Z99yJx.png)

* **Action :**
The ALU receives two inputs: the value from register `x2` and the sign-extended immediate value `32`,and performs the addition, calculating `x2 + 32`.In the upper-left image, we can see that the result in the ALU is `0x7FFFFFF0`, which is the result of `0x7FFFFFD0 + 32`. Additionally, observing the upper-right image, we can see that the value of `x2` has already been updated to `0x7FFFFFD0`.

**4. Memory Access (MEM)**
![螢幕擷取畫面 2024-10-14 211254](https://hackmd.io/_uploads/rJ0ytqc11e.png)

* **Action :**
    Since this is an `addi` instruction, no memory read or write is required. This stage is present in the pipeline but does not perform any actions for this instruction.


**5. Write Back (WB)**
![螢幕擷取畫面 2024-10-14 211408](https://hackmd.io/_uploads/ByZEY5qJ1e.png)![螢幕擷取畫面 2024-10-14 211226](https://hackmd.io/_uploads/r1u4K991Je.png)

* **Action**
    The result of the ALU calculation,`42`,is written back to the             destination register `x8`.
At this point, the value of `x8` is updated to `42`.




## Reference


[RISC-V](https://riscv.org/wp-content/uploads/2017/05/riscv-spec-v2.2.pdf)
