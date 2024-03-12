Fuzzing
========================

## Bash
    $(for i in $(seq 1 100); do echo -ne A; done)
    
## python

    $(python -c "print (100*'A')")
    
## ruby
    $(ruby -e 'puts "A" *100')
    
## perl
    $(perl -e 'print "A"x100')