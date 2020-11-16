---
title:  "FizzBuzz - The PowerShell Way"
date:   2020-11-16 18:10:00
categories: 
- Blog
tags:
  - PowerShell
  - FizzBuzz
---
### Solving the FizzBuzz challenge using PowerShell
The "FizzBuzz" challenge is a typical interview question designed to help determine job candidates solving skills to filter out job candidates.

**The challenge is the following**:

Print the numbers from  **1**  to  **100**  inclusive, each on their own line.

If, however, the number is a multiple of  **three**  then print  **Fizz**  instead, and if the number is a multiple of  **five**  then print  **Buzz**.

For numbers which are multiples of  **both three and five**  then print  **FizzBuzz**.


My approach:

````powershell
1..100 | foreach {

if($_ % 5 -eq 0 -and $_ % 3 -eq 0) {Write-Output "FizzBuzz"}

elseif($_ % 3 -eq 0) {Write-Output "Fizz"}

elseif($_ % 5 -eq 0) {Write-Output "Buzz"}

else {$_}

}
````

Write your own script, visit https://code.golf/fizz-buzz#powershell and give it a shot :blush:

