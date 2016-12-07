---
layout: post
title:  "[Java] Comparable 과 Comparator"
date:   2016-12-6 10:32:00 +0200
tags: ['Java', 'Comparable', 'Comparator']
author: "Jang chulwoon"
---


Java언어에서 정렬을 돕기위해 제공되는 Comparable , Comparator Interface에 대해 정리해보겠습니다.     
자세한 내용은 [Java doc]('https://docs.oracle.com/javase/8/docs/api/')을 참고해주세요.   

#Comparable    

Comparable Interface는 객체들을 정렬하기 위한 interface입니다.   
Comparable을 구현하고 있는 List와 배열은 자동적으로 Collection.sort에 의해 정렬되어질 수 있고, sort에 추가적인 비교자를 필요로하지 않습니다.   
+
만약 객체의 배열 또는 리스트를 정렬할때, Comparable을 구현하지 않고 sort Method를 호출하면 java.lang.ClassCastException 이 발생하네요.   

> comparable을 구현하고 있다면 해당 객체만 Array.sort()에 인자로 넣어주면 
자동적으로 정렬이 됩니다. 자세한 내용은 예제를 참고해주세요.            

Comparable은 compareTo라는 Method를 갖고있는데, 다음과 같습니다.   

```
	int compareTo(T o) 
```   

해당 Method는 -1,0,1 의 값을 반환하는데, 반환값이 0이면 두 수가 같음을 의미하고 인자로 들어온 값이 더 크면 -1을 , 작으면 1을 반환합니다.   

다음은 comparable을 구현한 예제입니다.    
	
	```
	
		public class Main {
		    public static void main(String[] args){
		        Student[] students = new Student[5];
		
		        Student Jang = new Student("장철운",50);
		        Student Kim = new Student("김씨",60);
		        Student Moon = new Student("문씨",40);
		        Student Jo = new Student("조씨",80);
		        Student Pack = new Student("박씨",70);
		        students[0] = Jang;
		        students[1] = Kim;
		        students[2] = Moon;
		        students[3] = Jo ;
		        students[4] = Pack;
		
		        Arrays.sort(students);
		
		        for(Student st : students){
		            System.out.println("성적 :"+st.getScore()+"\t 이름 : "+ st.getName());
		        }
		
		
		    }
		}
		class Student implements Comparable{
		    private int score;
		    private String name;
		
		    Student(String name, int score){
		        this.name = name;
		        this.score=score;
		    }
		    public int getScore(){
		        return score;
		    }
		    public String getName(){
		        return name;
		    }
		
		    @Override
		    public int compareTo(Object o) {
		        int input_score = ((Student)o).getScore();
		        // input_score - this.score 를 하면 내림차순으로 정렬할 수 있습니다.
		        return this.score - input_score;
		    }
		}
			
	
	```   
예제의 결과값은 다음과 같습니다. 

성적 :40	 이름 : 문씨   
성적 :50	 이름 : 장철운   
성적 :60	 이름 : 김씨   
성적 :70	 이름 : 박씨   
성적 :80	 이름 : 조씨   


compareTo 메소드는 if문을 사용하지 않고도 단순히 return this.score - input_score 처럼 사용할 수 있습니다.    

+
추가적으로 Array.sort()의 동작방식이 궁금해서 찾아봤습니다 .

	```
	    public static void sort(Object[] a) {
	        if (LegacyMergeSort.userRequested)
	            legacyMergeSort(a);
	        else
	            ComparableTimSort.sort(a, 0, a.length, null, 0, 0);
	    }
	```

jdk7 이상부터는 팀정렬로 , jdk6 이하 부터는 초기 합병정렬로 동작한다는 구문입니다.   
만약 초기합병정렬로 실행하고 싶다면, 다음과 같이 환경설정을 해야합니다. 
  -Djava.util.Arrays.useLegacyMergeSort=true       

> 시스템 프로퍼티로 실행할 경우엔 동작하지 않는다고 하네요.  

( 정렬과 관련해선 추후 정리하도록 하겠습니다. 간단히 팀 정렬은 개선된 합병정렬과 삽입정렬의 조합으로 만들어진 알고리즘이라고합니다. )

조금 더 깊게 들어가보면 다음과 같은 로직을 볼 수 있는데,    

	```
	        if (((Comparable) a[runHi++]).compareTo(a[lo]) < 0) { // Descending
	            while (runHi < hi && ((Comparable) a[runHi]).compareTo(a[runHi - 1]) < 0)
	                runHi++;
	            reverseRange(a, lo, runHi);
	        } else {                              // Ascending
	            while (runHi < hi && ((Comparable) a[runHi]).compareTo(a[runHi - 1]) >= 0)
	                runHi++;
	        }
	```

scomparable을 이용하여 정렬되는 걸 볼 수 있습니다. 


> 요약하면, comparable은 compareTo(T o) Method를 구현해야하고, 구현한 배열과 리스트는 Collection.sort 시 추가적인 기준을 제시하지 않아도 자동적으로 정렬 할 수 있습니다.    


#Comparator

Comparable과 마찬가지로 객체들을 정렬하기 위한 Interface입니다.   
Comparator 또한 다음과 같은 Method를 구현해야 합니다.   
( Comparable과 달리 인자를 2개 받는 Method 입니다. )   

	```
		int compare(T o1, T o2)
	```
우선 Comparator 의 예제를 보여드리겠습니다.

	```
		public class Main {
		    public static void main(String args[]){
		        Student[] students = new Student[5];
		        Student Jang = new Student("장철운",50);
		        Student Kim = new Student("김씨",60);
		        Student Moon = new Student("문씨",40);
		        Student Jo = new Student("조씨",80);
		        Student Pack = new Student("박씨",70);
		
		        students[0] = Jang;
		        students[1] = Kim;
		        students[2] = Moon;
		        students[3] = Jo ;
		        students[4] = Pack;
		        Arrays.sort(students, new StudentCompare());
		
		        for(Student st : students){
		            System.out.println("성적 :"+st.getScore()+"\t 이름 : "+ st.getName());
		        }
		    }
		
		}
		class StudentCompare implements Comparator<Student> {
		
		    @Override
		    public int compare(Student o1, Student o2) {
		        return o1.getScore() - o2.getScore();
		    }
		}
		class Student{
		    private int score;
		    private String name;
		
		    Student(String name, int score){
		        this.name = name;
		        this.score=score;
		    }
		    public int getScore(){
		        return score;
		    }
		    public String getName(){
		        return name;
		    }
		}

	```

Comparable의 예제와 비교했을 때 차이점 다음과 같습니다.   

클래스 내에서 정렬 기준을 설정   (comparable) / sort(객체)   
외부에서 정렬 기준을 설정  (comparator) / sort(객체,comparator)    

만약 comparable의 기준이 아닌 comparator 의 기준으로 정렬하고 싶다면   
sort(객체,comparator)을 사용하여 정렬이 가능합니다.    
사용 예시는 다음과 같습니다.
	
	```
		public class Main {
		    public static void main(String[] args){
		        Student[] students = new Student[5];
		
		        Student Jang = new Student("장철운",50);
		        Student Kim = new Student("김씨",60);
		        Student Moon = new Student("문씨",40);
		        Student Jo = new Student("조씨",80);
		        Student Pack = new Student("박씨",70);
		        students[0] = Jang;
		        students[1] = Kim;
		        students[2] = Moon;
		        students[3] = Jo ;
		        students[4] = Pack;
		
		        Arrays.sort(students);
		        System.out.println("comparable");
		        for(Student st : students){
		            System.out.println("성적 :"+st.getScore()+"\t 이름 : "+ st.getName());
		        }
		
		        System.out.println("comparator");
		        Arrays.sort(students,new StudentCompare());
		        for(Student st : students){
		            System.out.println("성적 :"+st.getScore()+"\t 이름 : "+ st.getName());
		        }
		        
		    }
		}
		
		class Student implements Comparable{
		    private int score;
		    private String name;
		
		    Student(String name, int score){
		        this.name = name;
		        this.score=score;
		    }
		    public int getScore(){
		        return score;
		    }
		    public String getName(){
		        return name;
		    }
		
		    @Override
		    public int compareTo(Object o) {
		        int input_score = ((Student)o).getScore();
		        // input_score - this.score 를 하면 내림차순으로 정렬할 수 있습니다.
		        return this.score - input_score;
		    }
		}

		class StudentCompare implements Comparator<Student> {
		
		    @Override
		    public int compare(Student o1, Student o2) {
		        String name1 = o1.getName().toUpperCase();
		        String name2 = o2.getName().toUpperCase();
		        return name1.compareTo(name2);
		    }
		}
	```
결과는 다음과 같습니다.

comparable   
성적 :40	 이름 : 문씨   
성적 :50	 이름 : 장철운   
성적 :60	 이름 : 김씨   
성적 :70	 이름 : 박씨   
성적 :80	 이름 : 조씨   
comparator   
성적 :60	 이름 : 김씨   
성적 :40	 이름 : 문씨   
성적 :70	 이름 : 박씨   
성적 :50	 이름 : 장철운   
성적 :80	 이름 : 조씨   

예시는 comparable 시엔 score을 기준으로, comparator 시엔 name을 기준으로 정렬하였습니다.   

> 즉 기존에 정렬 방식이 아닌 새로운 기준의 정렬을 행할 경우 comparator을 정의하여 사용하면 됩니다.    


이상 Comparator 와 Comparable의 정리를 끝내도록 하겠습니다.   
예시를 많이두어 이해하기 쉽도록 정리해보았습니다.   



참고자료   
[Java doc]('https://docs.oracle.com/javase/8/docs/api/')   
[Java object sorting example ]('https://www.mkyong.com/java/java-object-sorting-example-comparable-and-comparator/')


lusiue@gmail.com    
2016-12-6 장철운. 


