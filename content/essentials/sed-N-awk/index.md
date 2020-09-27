---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "sed & awk Tutorial"
subtitle: ""
summary: "Basics of sed and awk scripting."
authors: []
tags: []
categories: []
date: 2020-09-27T15:51:19+05:30
lastmod: 2020-09-27T15:51:19+05:30
featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---

**Credits -** These are my notes from [Richard Bohm's Udemy Class](https://www.udemy.com/course/linux-bash-shell-scripting-complete-guide-incl-awk-sed/).

## SED
------------------------------------------------------

*  SED is a stream editor.

#### Structure :
* sed OPTIONS ... [SCRIPT] [INPUTFILE....]
* cat [INPUTFILE] | sed OPTIONS ... [SCRIPT]
* SCRIPT:
    * ``` [addr]X[options] ```
        * ``` [addr] ``` - can be a single line, number, a regular expression, or range of lines. If ``` [addr] ``` is specified, the command X will be executed only on the matched lines.
        * ``` X ``` - single-letter sed command.
        * Additional ```[options]``` for sed command.  

    *  Eg:  ```sed '30,35d' infile.txt > outfile.txt ```
        * Delete range of lines Line 30 - line 35 from infile and save output to outfile.txt
        * ```d``` - is the delete command
* Sed by default does not alter the inputfile, only prints. 
* Sed accepts one single command at a time. To use multiple commands use ``` -e ``` option

*  3 ways to specify multiple instructions:
   * Using ```;``` . Eg : ``` sed 's/abc/replace/; s/pqr/replace2/' inputfile ```
   * Using the precedence '-e' . Eg:   ``` sed 's/abc/replace/' -e  's/pqr/replace2/' inputfile ```
   * Multiline entry:
        
                $ sed '
                > s/Red/Ray/
                > s/Dev/Development/
                > ' emp.txt

#### Example file to be used throughout : emp.txt

        cat emp.txt

        output:
        Name Age Unit
        Ant  23  IT
        Bec  25  HT
        Red  34  CEO
        Dua  32  FIN
        Wui  29  PR
        Wui  29  PR
        Van  27  Dev
        Kim  26  Dev

#### Sed Commands :

* ``` a ``` -  Append text after a line.
    * eg: Append after IT some text : ```  sed '/IT/a QWE  ## XY' emp.txt ```

                output:
                Name Age Unit
                Ant  23  IT
                QWE  ## XY
                Bec  25  HT
                Red  34  CEO
                Dua  32  FIN
                Wui  29  PR
                Van  27  Dev
                Kim  26  Dev

* ``` i 'text' ``` - insert text before a line
    * eg: Insert before IT some text : ```  sed '/IT/i QWE  ## XY' emp.txt ```

                output:
                Name Age Unit
                QWE  ## XY
                Ant  23  IT
                Bec  25  HT
                Red  34  CEO
                Dua  32  FIN
                Wui  29  PR
                Van  27  Dev
                Kim  26  Dev


* ``` d ``` - delete the pattern
    * eg: Delete all lines containing 'Dev' : ```  sed '/Dev/d' emp.txt ```

            output:
            Name Age Unit
            Ant  23  IT
            Bec  25  HT
            Red  34  CEO
            Dua  32  FIN
            Wui  29  PR


* ``` p  ``` - print the pattern.
    * eg:  ```  sed -n '/Dev/p' emp.txt ``` '-n' is used to explicitly disable print all, as sed prints all by default. 
            
            output:
            Van  27  Dev
            Kim  26  Dev

    * eg: "Print 2nd line"  ```sed -n '2p' emp.txt ``` 

            output:
            Ant  23  IT

    * eg: "Print from line 2 to 5"  ```sed -n '2,5p' emp.txt ``` 

            output:
            Ant  23  IT
            Bec  25  HT
            Red  34  CEO
            Dua  32  FIN
    
* ``` c ``` - Change command used to change lines.
    * eg: Change all lines with 'Dev' to XXXXXX... - ```  sed '/Dev/c xxxxxxxxxxxxxxxxxxxxxxx' emp.txt ```

         
             output:
            Name Age Unit
            Ant  23  IT
            Bec  25  HT
            Red  34  CEO
            Dua  32  FIN
            Wui  29  PR
            xxxxxxxxxxxxxxxxxxxxxxx
            xxxxxxxxxxxxxxxxxxxxxxx

* ``` q[exit-code] ``` - exit sed without processing any more commands or input.
    * eg:  ```sed '/Bec/q2' emp.txt``` will quit once the search pattern matches and will receive exit status code 2. ```echo $?``` gives an output of ``` 2```

            output:
            Name Age Unit
            Ant  23  IT
            Bec  25  HT


* ``` s/regexp/replacement/[flags]``` - (substitute) Match the regex against the content of the pattern space. If found replace matched string with 'replacement'. Use ```g ``` to substitute globally. 
    * Eg: ``` echo "1110000111" | sed 's/000/XXX/' ``` --> output:  ``` 111XXX0111 ```
    * Eg: ```  echo "The quick brown fox  , trots away     ...  " | sed 's/[[:space:]]/#/g' ```. This will replaces all spaces globally. Output : ``` The#quick#brown#fox##,#trots#away#####...## ```
    * Eg: ``` echo "The quick brown fox  , trots away     ...  " | sed 's/[[:space:]]/#/ ``` . Without the global flag (g) will simply replace the first instance. --> output: ``` The#quick brown fox  , trots away     ...   ```
    * Eg: Replace specific lines in a file. ``` sed '8s/Dev/Manager/' emp.txt ``` --> Replace 'Dev' with 'Manager' on the 8th line.

            output:
            Name Age Unit
            Ant  23  IT
            Bec  25  HT
            Red  34  CEO
            Dua  32  FIN
            Wui  29  PR
            Van  27  Dev
            Kim  26  Manager
    
    * Eg: Replace  all lines we use range ``` 1,$ ```. Use command ``` sed '1,$s/e/EE/' emp.txt ``` to replace all instances of 'e' with 'EE'.

            output:
            NamEE Age Unit
            Ant  23  IT
            BEEc  25  HT
            REEd  34  CEO
            Dua  32  FIN
            Wui  29  PR
            Van  27  DEEv
            Kim  26  DEEv


* Command-Line- Options:
    * ``` -n ``` - disable automatic printing; sed produced output when explicitlytold via the p command.
    * ``` -e script ``` - add script
        * eg:  Search some text and quit with exit code 2 : ``` sed -ne '/CEO/p' -ne '/CEO/q2' emp.txt  ``` and ``` echo $? ``` will return 2.

                output:
                Red  34  CEO

        * eg: Print "000- before/after -000" before and after lines containing 'Dua' :  ``` sed -e '/Dua/a 000- after -000' -e '/Dua/i 000- before -000' emp.txt  ```

                output:
                Name Age Unit
                Ant  23  IT
                Bec  25  HT
                Red  34  CEO
                000- before -000
                Dua  32  FIN
                000- after -000
                Wui  29  PR
                Van  27  Dev
                Kim  26  Dev

    * ``` -r ``` - use extended regular expressions rather than basic regular expressions.

    * ``` -i ``` - Use this flag to modify the input file. 
        * We take a copy of emp.txt as emp2.txt and perform the following :  ```  sed -ni '/Wui/p' emp2.txt ```
        * ``` cat emp2.txt ``` :

                output:
                Wui  29  PR

        
    *  ``` e ``` To run scripts. This is different than ``` -e ```
        * ``` sed '/Name/e echo -n "Date:"; date' emp.txt ```  - Run and print date before 'Name'.

                output:
                Date:Sat Aug 29 08:00:34 IST 2020
                Name Age Unit
                Ant  23  IT
                Bec  25  HT
                Red  34  CEO
                Dua  32  FIN
                Wui  29  PR
                Van  27  Dev
                Kim  26  Dev

        * ``` sed '1 e echo -n "Date:"; date' emp.txt ``` - Print date on the first line.

                output:
                Date:Sat Aug 29 08:05:41 IST 2020
                Name Age Unit
                Ant  23  IT
                Bec  25  HT
                Red  34  CEO
                Dua  32  FIN
                Wui  29  PR
                Van  27  Dev
                Kim  26  Dev

        * ```  sed '1,$ e echo -n "Date:"; date' emp.txt ``` - Print date before every line.

                output:
                Date:Sat Aug 29 08:16:09 IST 2020
                Name Age Unit
                Date:Sat Aug 29 08:16:09 IST 2020
                Ant  23  IT
                Date:Sat Aug 29 08:16:09 IST 2020
                Bec  25  HT
                Date:Sat Aug 29 08:16:09 IST 2020
                Red  34  CEO
                Date:Sat Aug 29 08:16:09 IST 2020
                Dua  32  FIN
                Date:Sat Aug 29 08:16:09 IST 2020
                Wui  29  PR
                Date:Sat Aug 29 08:16:09 IST 2020
                Van  27  Dev
                Date:Sat Aug 29 08:16:09 IST 2020
                Kim  26  Dev
                Date:Sat Aug 29 08:16:09 IST 2020
                
                
 #### Sed Commands :
Its not advised to have long sed scripts on commndline, a better option is to  create a script file. 

 * Syntax:  sed -f scriptfile inputfile. Where scriptfile contains the sed instructions. 


 ------------------------

 ## AWK 
------------------------------------------------------

Searches files for patterns and performs actions specified in the AWK body. 

#### Structure :
* ``` awk'program_to_perform_action' file1 file2 ...```
* Divided into 3 sections BEGIN, Main & END
* BEGIN - Code specified here is executed before executing the operations on the file.
* Main - Executed for ech line of the file.
* END - After awk process of all lines.
 
       awk 'BEGIN{
           code_in_BEGIN_section}
       {Code_in_Main_Body}
       END{
           code_END_Section }' file1 file2 ...
         
Example:
        
        echo "one two three" | awk'BEGIN{begin_code}{main_code}END{end_code}'
    
Example 2:

        echo "one two three" | awk'{main_code}'

### [NOTE] In AWK body, Bash features DO NOT WORK. AWK has its own syntax.

* Hello World: ``` awk 'BEGIN{print "Hello world !"}' ```
* To print "Hello World !" on each line after Enter ``` awk '{print "Hello world !"}' ``` 
* Example: ``` echo "This is one line" | awk 'BEGIN{print "start"}{print "OK"}END{print "stop"}' ``` The output is as shown below. OK is printed once as there is just one line.

        Output:
        start
        OK
        stop
      
* Example (lets input a file hello.txt which has 3 lines): ``` cat hello.txt | awk 'BEGIN{print "start"}{print "OK"}END{print "stop"}' ``` OR ```awk 'BEGIN{print "start"}{print "OK"}END{print "stop"}' hello.txt ```

        output:
        start
        OK
        OK
        OK
        stop

### Fields :

* Fields are by default seperated by space.
* ``` $0 ``` prints entire line
* ``` $1 ``` prints the first field and so on ..
* Examples: 
    * ```echo "1 2 3 4 5" | awk '{print $0}'``` will output ``` 1 2 3 4 5 ```
    * ```echo "1 2 3 4 5" | awk '{print $1}'``` will output ``` 1```
    * ```echo "1 2 3 4 5" | awk '{print $3}'``` will output ``` 3 ```   

* Examples with a File (emp.txt)
* To print all columns in the file ``` awk '{print $0}' emp.txt```
    
    Output: 

       Name Age Unit
        Ant  23  IT
        Bec  25  HR
        Red  34  CEO
        Dua  32  FIN
* ``` awk '{print $1}' emp.txt```

        Name
        Ant
        Bec
        Red
        Dua

* ``` awk '{print $2}' emp.txt```

        Age
        23
        25
        34
        32

* ``` awk '{print $3}' emp.txt```

        Unit
        IT
        HR
        CEO
        FIN
* ``` awk '{print $1,$3}' emp.txt```

        Name Unit
        Ant IT
        Bec HR
        Red CEO
        Dua FIN


### Search Patterns : 

Awk performs operations line by line. Search pattern is defined between '//'.
AWK uses the default Regular Expressions

eg 1 :   ``` awk ' /CEO/ {print $1,$3}' emp.txt```

    output:
        Red CEO


### NF - Number of Fields : 

* ``` echo "1 two 3 four" | awk '{print NF}' ``` Output : ``` 4 ```
* ``` echo "1 two 3 four" | awk '{print $(NF-2)}' ``` Output : ```two```. 
* $NF = 4 and using this we could do mathemetical operations. Eg. To print the second last field we could '{print $(NF-1)}'

### NR - Number of Records :

Records in Awk are by default seperated by a newline.

Eg 1: ```  echo "1 two 3 four" | awk '{print $(NR)}' ``` . Output : ```1```

Eg 2:  ``` awk '{print NR}' emp.txt```

    Output:
        1
        2
        3
        4
        5
        6

As ```awk``` processess line by line, for each line it prints the nos. of records found. 

To print exact records from a file we could use ``END``.
Eg: ```awk 'END{print NR}' emp.txt```. Output : ```6```


### FS - Field Seperator :

Default is space. We can define custom values for the field seperator. 

* Eg: ```echo "102 202 303" | awk 'BEGIN{FS="0"} {print $1"-"$2"-"$3"-"$4}' ``` . Output of the above command : ```  1-2 2-2 3-3``` . We used ```0``` as FS.

### RS - Record Seperator :

By default seperated by newline. Can devine custom values for RS (record seperator).

* Eg: ``` echo "102 202 303" | awk 'BEGIN{RS="0"} END{print NR}' ``` . Output is ```4```.
* Now the default RS would return 1. as there is only one line. Eg. ``` echo "102 202 303" | awk 'END{print NR}' ```  Output : ```1```


### AWK Variables :

* Assignment :
    * ``` a=1 ```
    * ``` RS="\t" ```
    * ``` FS=":" ```

* Increment/ Decrement :
    * ``` a++ / a-- ```
    * ``` a=a+1 / a=a-1```

* Math Operations :
    * ``` a=b+c ``` add
    * ``` a=b*c ``` multiply
    * ``` a=b/c ``` divide
    * ``` a=b-c ``` subtract
    * ``` a=b%c ``` Modulus 
    * ``` a=b^c ``` Raise var to the power
    * ``` a=b**c ``` Raise var to the power


### AWK - if Statement

        if(condition){
                command(s)
        }
        else{
            command(s)    
        }

* Comparisons:
  *  ``` == ``` , ``` < ```, ``` <= ```, ``` > ```, ``` >= ```, ``` != ```
  * eg: ``` awk '{if ($1 == "Red"){print $1, $2, $3}}' emp.txt ```. Output would be : ``` Red  34  CEO ```. 
  * OR if we want to print all fields we could also use $0 instead. Eg: ``` awk '{if ($1 == "Red"){print $0}}' emp.txt ``` Output :  ``` Red  34  CEO ```.


### AWK For loops

Structure:
   
    for (initialization; condition; increment){
            command(s)
    }


* Eg: ``` awk 'BEGIN{for(i=1; i<=3; i++){print "test -", i}}' ``` 
Output: 

        test - 1
        test - 2
        test - 3

### AWK Script files :

* Structure :  ``` awk -f awkscript inputfiles```