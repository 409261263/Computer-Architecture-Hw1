Quize 1 Problem C



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



Problem C (Risc-v assembly code)



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


Problem C(Risc-v assembly code with loop unrolling)



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






LeetCode 342 (Power of Four)


LeetCode 342 C code


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


LeetCode 342 RISC-V assembly code


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



LeetCode 342 RISC-V assembly code with loop unrolling



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
