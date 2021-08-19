---
layout: post
title:  "Can you break an IF statement in Java"
date:   2021-08-20 00:03 +0100
categories: shorts
tags:
- java
- spec
- documentation
---


Can you use `break` with an `if` statement in Java?

Without examining whether you should, **yes**, you can use labeled statements with `if` in Java. According to the [1.7 specification][spec]
    
    
>The Identifier is declared to be the label of the immediately contained Statement. [...] identifier statement labels are used with [break][break] (§14.15) or [continue][cont] (§14.16) statements appearing anywhere within the  labeled statement.
        
It goes on (emphasis added)
        
> If the statement is labeled by an Identifier and the contained Statement completes abruptly because of a break with the same Identifier, then **the labeled statement completes normally**. In all other cases of abrupt completion of the Statement, the labeled statement completes abruptly for the same reason.
        
So if you `break` an `if` block (remember a block is a statement), you can exit the `if` body. Let's test it:
    
    
    public static void main(String[] args) {
        if (true) label: {
            if (args != null)
                break label;
            System.out.println("doesn't get here");
        }
        System.out.println("Outside of labeled block");
    }
    
        
Output:
    
    
    Outside of labeled block
    
    
[spec]: https://docs.oracle.com/javase/specs/jls/se7/html/jls-14.html#jls-14.7
[break]: https://docs.oracle.com/javase/specs/jls/se7/html/jls-14.html#jls-14.15
[cont]: https://docs.oracle.com/javase/specs/jls/se7/html/jls-14.html#jls-14.16
