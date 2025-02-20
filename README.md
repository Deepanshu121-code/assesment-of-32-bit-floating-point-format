# assesment-of-32-bit-floating-point-format
def decimal_to_ieee754(decimal_value):
    """Converts a decimal value to its IEEE 754 32-bit floating-point representation.

    Args:
        decimal_value: The decimal number to convert.

    Returns:
        A string representing the 32-bit IEEE 754 representation (e.g., "0 10000000 00000000000000000000000").
        Returns an error message if the input is not a valid number.
    """
    try:
        decimal_value = float(decimal_value)
    except ValueError:
        return "Invalid input: Not a valid number."


    if decimal_value == 0:  # Special case for zero
        return "0 00000000 00000000000000000000000"

    sign = 1 if decimal_value < 0 else 0
    decimal_value = abs(decimal_value)

    integer_part, fractional_part = str(decimal_value).split(".")  #handles integers too, fractional will be empty

    integer_binary = bin(int(integer_part))[2:]
    fractional_binary = ""

    fractional_part = float("0." + fractional_part)


    while fractional_part > 0 and len(fractional_binary) < 23:  # Limit to 23 fractional bits
        fractional_part *= 2
        if fractional_part >= 1:
            fractional_binary += "1"
            fractional_part -= 1
        else:
            fractional_binary += "0"
            


    if integer_binary:  # Normalized case
        exponent = len(integer_binary) - 1
        mantissa = integer_binary[1:] + fractional_binary
        mantissa = mantissa[:23] # Truncate or pad with zeros to 23 bits

    else: #Denormalized case
      exponent=0
      first_one= fractional_binary.find('1')

      if first_one == -1:
          return "0 00000000 00000000000000000000000" #handle 0 case, though we've already done a 0 check



      exponent = - (first_one +1) 

      mantissa = fractional_binary[first_one+1:]
      mantissa = mantissa[:23] # Truncate or pad with zeros to 23 bits



    biased_exponent = exponent + 127

    exponent_binary = bin(biased_exponent)[2:].zfill(8)



    return f"{sign} {exponent_binary} {mantissa.ljust(23, '0')}"  # Pad mantissa with zeros



# Get input from the user
decimal_input = input("Enter a decimal number: ")

# Convert and print the IEEE 754 representation
ieee754_representation = decimal_to_ieee754(decimal_input)
print(ieee754_representation)
