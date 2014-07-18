---
layout:		post
title:		Java中循环跳出的区别
description: 最近买了本李刚的《疯狂Java讲义》，还不错，刚开始看。虽然不知道能不能坚持看完，但是还是把博客的写作坚持下去。
category:	blog
---

1.return 跳出returnAndBreak方法，无视循环层级，直接跳出各种循环

	// 演示示例  
	public void returnAndBreak() {  
	    int i = 0;  
	    String interleave = "ON";  
	      
	    System.out.println("==== return ====");  
	    while (i < 10) {  
	        System.out.println(" OUT==================== ");  
	        i ++ ;  
	        if ( interleave.equalsIgnoreCase("ON") ) {  
	            return;  
	        }  
	        System.out.println("i = " + i);  
	    }  
	    System.out.println("==== return ====");  
	}  
	// 输出结果  
	==== return ====  
	OUT====================   

2.break 跳出while (i < 10) {} 循环
break 用于完全结束一个循环，跳出一个循环体

	// 演示示例  
	public void returnAndBreak() {  
	    int i = 0;  
	    String interleave = "ON";  
	      
	    System.out.println("==== break ====");  
	    while (i < 10) {  
	        System.out.println(" ==================== ");  
	        i ++ ;  
	        if ( interleave.equalsIgnoreCase("ON") ) {  
	            break;  
	        }  
	        System.out.println("i = " + i);  
	    }  
	    System.out.println("==== break ====");  
	}  
	// 输出结果：  
	==== break ====  
	 ====================   
	==== break ====  

break还可以结束外层循环。如下：当j == 2的时候跳出循环到outer标记处

	public static void main(String[] args) {
	        outer:
	        for (int i = 0; i < 5; i++) {
	            for (int j = 0; j < 3; j++) {
	                System.out.println("i is " + i + " j is " + j);
	                if (j == 2) {
	                    break outer;
	                }
	            }
	        }
	    }
3.if条件判断 + continue 中断循环进行下一次循环(此例演示的加条件判断后，如果条件满足，后面的步骤就不执行了)

	// 演示示例  
	public void returnAndBreak() {  
	    int i = 0;  
	    String interleave = "ON";  
	      
	    System.out.println("==== continue ====");  
	    while (i < 10) {  
	        System.out.println(" ==================== ");  
	        i ++ ;  
	        if ( interleave.equalsIgnoreCase("ON") ) {  
	            continue;  
	        }  
	        System.out.println("i = " + i);  
	    }  
	    System.out.println("==== continue ====");  
	}  
	输出结果：  
	==== continue ====  
	 ====================   
	 ====================   
	 ====================   
	 ====================   
	 ====================   
	 ====================   
	 ====================   
	 ====================   
	 ====================   
	 ====================   
	==== continue ====  

