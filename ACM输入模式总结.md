# 1. **多⾏输⼊，每⾏两个整数**

https://kamacoder.com/problempage.php?pid=1000

```java
import java.lang.*;
import java.util.*;
public class Main{
    public static void main(String[] args){
        Scanner in = new Scanner(System.in);
        while(in.hasNextInt()){
            int a = in.nextInt();
            int b = in.nextInt();
            System.out.println(a+b);
        }
    }
}
```

# 2.  **多组数据，每组第⼀⾏为n, 之后输⼊n⾏两个整数**

https://kamacoder.com/problempage.php?pid=1001

```java
import java.util.Scanner;
public class Main {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        while (scanner.hasNext()) {
            int n = scanner.nextInt();
            while (n-- > 0) {
                int a = scanner.nextInt();
                int b = scanner.nextInt();
                System.out.println(a + b);
            }
        }
    }
}
```

#  3. **若⼲⾏输⼊，每⾏输⼊两个整数，遇到特定条件终⽌**

https://kamacoder.com/problempage.php?pid=1002

```java
import java.util.Scanner;
public class Main {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        while (scanner.hasNext()) {
            int a = scanner.nextInt();
            int b = scanner.nextInt();
            if (a == 0 && b == 0) {
                break;
            }
            System.out.println(a + b);
        }
    }
}
```

# 4. 若干行输入，遇到0终止，每行第一个数为N，表示本行后面有N个数

https://kamacoder.com/problempage.php?pid=1003

```java
import java.util.Scanner;
public class Main {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        while (scanner.hasNext()) {
            int n = scanner.nextInt();
            if (n == 0) {
                break;
            }
            int sum = 0;
            for (int i = 0; i < n; i++) {
                sum += scanner.nextInt();
            }
            System.out.println(sum);
        }
    }
}
```

# 5. **若⼲⾏输⼊，每⾏包括两个整数**a和b，由空格分隔，每⾏输出后接⼀个空⾏。

https://kamacoder.com/problempage.php?pid=1004

```java
import java.util.Scanner;
public class Main{
    public static void main(String[] args){
        Scanner sc = new Scanner(System.in);
        while(sc.hasNextLine()){
            int a = sc.nextInt();
            int b = sc.nextInt();
            System.out.println(a + b);
            System.out.println();
        }
    }
}
```

# 6. 多组n⾏数据，每⾏先输⼊⼀个整数N，然后在同⼀⾏内输⼊M个整数,每组输出之间输出⼀个空⾏

https://kamacoder.com/problempage.php?pid=1005

```java
import java.util.Scanner;
public class Main{
    public static void main(String[] args){
        Scanner sc = new Scanner(System.in);
        while(sc.hasNextLine()){
            int N = sc.nextInt();
            // 每组有n⾏数据
            while(N-- > 0){
                int M = sc.nextInt();
                int sum = 0;
                // 每⾏有m个数据
                while(M-- > 0){
                    sum += sc.nextInt();
                }
                System.out.println(sum);
                if(N > 0) System.out.println();
            }
        }
    }
}
```

# 7. 多组测试样例，每组输⼊数据为字符串，字符⽤空格分隔，输出为⼩数点后两位

https://kamacoder.com/problempage.php?pid=1006

```java
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner in = new Scanner(System.in);
        while (in.hasNextLine()) {
            String line = in.nextLine(); // 接收⼀整⾏字符串作为输⼊
            String[] items = line.split(" "); // 字符串分割成数组
            for (String item : items) { // 遍历数组
            }
        }
    }
}
```

# 8.  多组测试⽤例，第⼀⾏为正整数n, 第⼆⾏为n个正整数，n=0时，结束输⼊，每组输出结果的下⾯都输出⼀个空⾏

https://kamacoder.com/problempage.php?pid=1007

```java
import java.util.ArrayList;
import java.util.Scanner;
public class Main {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        while (scanner.hasNext()) {
            Integer size = scanner.nextInt();
            if (size == 0) {
                break;
            }
            // 创建list
            ArrayList<Integer> list = new ArrayList<>();
            // 添加⼀组数据到list中
            for (int i = 0; i < size; i++) {
                int num = scanner.nextInt();
                list.add(num);
            }
            // 遍历
            for (int i = 0; i < list.size(); i++) {
                System.out.println(list.get(i));
            }
            System.out.println(res);
            System.out.println();
        }
    }
}
```

# 9. **多组测试数据，每组数据只有⼀个整数，对于每组输⼊数据，**输出⼀⾏，每组数据下⽅有⼀个空⾏

https://kamacoder.com/problempage.php?pid=1008

```java
import java.util.*;
public class Main {
    public static void main(String[] args) {
        Scanner in = new Scanner(System.in);
        while (in.hasNextInt()) {
            int n = in.nextInt();
            while (n > 0) {
                int tmp = n % 10; // 获取各位数据
                n /= 10;
            }
            System.out.println(res);
            System.out.println();
        }
    }
}
```

# 10. 多组测试数据，每个测试实例包括2个整数M，K（2<=k<=M<=1000)。M=0，K=0代表输⼊结束

https://kamacoder.com/problempage.php?pid=1009

```java
import java.util.*;
public class Main{
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        while (sc.hasNextInt()) {
            int m = sc.nextInt();
            int k = sc.nextInt();
            if (m == 0 && k == 0) break;
            int sum = 0;
            System.out.println(sum);
        }
    }
}
import java.util.*;

public class Main{
    public static void main (String[] args) {
        
        Scanner sc = new Scanner(System.in);
        while(sc.hasNext()){
            int m = sc.nextInt();
            int k = sc.nextInt();
            if(m == 0 & k == 0) break;
            int res = m;
            while(m >= k){
                res += m / k;
                m = m / k + m % k;
            }
            System.out.println(res);
        }
    }
}
```

# 11. 多组测试数据，⾸先输⼊⼀个整数N，接下来N⾏每⾏输⼊两个整数a和b, **读取输⼊数据到**Map

```java
import java.util.*;
public class Main{
    static Map<Integer, Integer> map = new HashMap();
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        while (sc.hasNextInt()) {
            int n = sc.nextInt();
            for (int i = 0; i < n; i++) {
                int a = sc.nextInt();
                int b = sc.nextInt();
                map.put(a, b);
            }
        }
    }
}
import java.util.*;

public class Main{
    public static void main (String[] args) {
        /* code */
        Scanner sc = new Scanner(System.in);
        while(sc.hasNext()){
            int n = sc.nextInt();
            Map<Integer, Integer> map = new HashMap<>();
            while(n -- > 0){
                int son = sc.nextInt();
                int father = sc.nextInt();
                map.put(son,father);
            }
            int count1 = 0, count2 = 0;
            int start1 = 1, start2 = 2;
            while(map.containsKey(start1)){
                start1 = map.get(start1);
                count1++;
            }
            while(map.containsKey(start2)){
                start2 = map.get(start2);
                count2++;
            }
            if(count1 > count2){
                System.out.println("You are my elder");
            }else if(count1 < count2){
                System.out.println("You are my younger");
            }else{
                System.out.println("You are my brother");
            }
        }
    }
}
```

# 12. 多组测试数据。每组输⼊⼀个整数n，输出特定的数字图形

```java
import java.util.Scanner;
public class Main {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        while (scanner.hasNext()) {
            int n = scanner.nextInt();
            for (int i = 1; i <= n; i++) {
                print(n - i, i);
            }
            for (int i = n - 1; i >= 1; i--) {
                print(n - i, i);
            }
        }
    }
    public static void print(int blank, int n) {
        // 前⾯需要补⻬空格
        for (int i = 0; i < blank; i++) {
            System.out.print(" ");
        }
        for (int i = 1; i <= n; i++) {
            System.out.print(i);
        }
        for (int i = n - 1; i > 0; i--) {
            System.out.print(i);
        }
        System.out.println();
    }
}
```

# 13. 多⾏输⼊，每⾏输⼊为⼀个字符和⼀个整数，遇到特殊字符结束

https://kamacoder.com/problempage.php?pid=1012

```java
import java.util.Scanner;
public class Main {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        while (sc.hasNext()) {
            String line = sc.nextLine();
            if (line.equals("@"))
                break;
            String[] inputs = line.split(" ");
            char ch = inputs[0].charAt(0);
            int n = Integer.parseInt(inputs[1]);
        }
        sc.close();
    }
}
```

# 14. :warning:第⼀⾏是⼀个整数n，表示⼀共有n组测试数据, 之后输⼊n⾏字符串

https://kamacoder.com/problempage.php?pid=1013

在使用 `nextInt` 后，如果接下来要读取字符串，可能会遇到换行符的问题

需要`nextLine()`一下

```java
import java.util.*;
public class Main{
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        while (sc.hasNextInt()) {
            int n = sc.nextInt();
            sc.nextLine();
            for (int i = 0; i < n; i++) {
                String line = sc.nextLine().trim();
                StringBuilder sb = new StringBuilder();
                System.out.println(sb.toString());
            }
        }
    }
}
```

# 15. 第⼀⾏是⼀个整数n，然后是n组数据，每组数据2⾏，每⾏为⼀个字符串，为每组数据输出⼀个字符串，每组输出占⼀⾏

https://kamacoder.com/problempage.php?pid=1014

```java
import java.util.Scanner;
public class Main{
    public static void main(String[] args) {
        Scanner in = new Scanner(System.in);
        int n = in.nextInt();
        for (int i = 0; i < n; i++) {
            String a = in.next();
            String b = in.next();
            StringBuilder sb = new StringBuilder(a.substring(0,a.length()/2));
            sb.append(b);
            sb.append(a.substring(a.length() / 2));
            System.out.println(sb.toString());
        }
    }
}
```

# 16. :warning:多组测试数据，第⼀⾏是⼀个整数n，接下来是n组字符串，输出字符串

https://kamacoder.com/problempage.php?pid=1015

```java
import java.util.*;

public class Main{
    public static void main(String[] args){
        Scanner sc = new Scanner(System.in);
        int count = sc.nextInt();
        sc.nextLine();
        while(count-- > 0){
            String str = sc.nextLine();
            StringBuffer s = new StringBuffer();
            for(int i = 0;i < str.length();i+=2){
                s.append(str.charAt(i + 1));
                s.append(str.charAt(i));
            }
            System.out.println(s.toString());
        }
    }
}
```

# 17. 多组测试数据，每组测试数据的第⼀⾏为整数N（1<=N<=100），当N=0时，输⼊结束，第⼆⾏为N个正整数，以空格隔开，输出结果为字符串

https://kamacoder.com/problempage.php?pid=1016

```java
import java.util.Scanner;
import java.util.Stack;
import java.util.*;

public class Main {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        while (scanner.hasNext()){
            int n = Integer.parseInt(scanner.nextLine());   // 入栈顺序 1~n
            if (n == 0) break;
            String[] sequenceArr = scanner.nextLine().split(" ");   // 出栈序列
            int index = 0;  // 出栈序列的索引
            Deque<Integer> stack = new ArrayDeque<>();

            for (int j = 1; j <= n; j++) {  // 1-n入栈，入栈后判断出栈序列是否正确
                stack.push(j);

                // 当前出栈队列的索引位置 等于 栈顶元素时 出栈并使出栈队列索引+1
                while (!stack.isEmpty() && stack.peek() == Integer.parseInt(sequenceArr[index])) {
                    stack.pop();
                    index++;
                }
            }

            // 最后看栈是否为空则得知出栈序列是否正确
            System.out.println(stack.isEmpty() ? "Yes" : "No");
        }

        scanner.close();
    }
}
```

# 18. ⼀组输⼊数据，第⼀⾏为n+1个整数，逆序插⼊n个整数，第⼆⾏为⼀个整数m, 接下来有m⾏字符串，并根据字符串内容输⼊不同个数的数据

https://kamacoder.com/problempage.php?pid=1017

```java
public static void main(String[] args) {
    Scanner sc = new Scanner(System.in);
    // 输⼊n
    int n = sc.nextInt();
    // 输⼊n个整数
    for (int i = 0; i < n; i++) {
        int num = sc.nextInt();
        linkedList.addFirst(num);
    }
    // 输⼊m
    int m = sc.nextInt();
    // 输⼊m个字符串
    for (int i = 0; i < m; i++) {
        // 获取输⼊的字符串
        String operation = sc.next();
        // 根据输⼊内容，给出不同输出结果
        if ("get".equals(operation)) {
            int a = sc.nextInt();
            int result = linkedList.get(a - 1);
            if (result != -1) {
                System.out.println(result);
            } else {
                System.out.println("get fail");
            }
        } else if ("delete".equals(operation)) {
            int a = sc.nextInt();
            boolean deleteResult = linkedList.delete(a - 1);
            if (deleteResult) {
                System.out.println("delete OK");
            } else {
                System.out.println("delete fail");
            }
        } else if ("insert".equals(operation)) {
            int a = sc.nextInt();
            int e = sc.nextInt();
            boolean insertResult = linkedList.insert(a - 1, e);
            if (insertResult) {
                System.out.println("insert OK");
            } else {
                System.out.println("insert fail");
            }
        } else if ("show".equals(operation)) {
            linkedList.show();
        }
    }
    sc.close();
}
```

# 19. 多组测试数据，每⾏为n+1个数字， 输出链表或对应的字符串

https://kamacoder.com/problempage.php?pid=1018

```java
import java.util.Scanner;
public class Main{
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        while (sc.hasNextLine()) {
            String[] str = sc.nextLine().split(" ");
            if (Integer.parseInt(str[0]) == 0) {
                System.out.println("list is empty");
            }
            ListNode dummyhead = new ListNode(-1);
            ListNode cur = dummyhead;
            //构造链表
            for (int i = 1; i < str.length; i++) {
                ListNode temp = new ListNode(Integer.parseInt(str[i]));
                cur.next = temp;
                cur = cur.next;
                if (i == str.length - 1) cur.next = null;
            }
            //输出原链表
            ListNode pointer = dummyhead.next;
            while (pointer != null) {
                System.out.print(pointer.val + " ");
                pointer = pointer.next;
            }
            System.out.println();
        }
    }
}
```

# 20.  **多组输⼊，每组输⼊包含两个字符串，输出字符串**

https://kamacoder.com/problempage.php?pid=1020

```java
public class Main{
    public static Map<Character, Integer> map = new HashMap();
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        while (sc.hasNextLine()) {
            String s = sc.nextLine();
            String[] ss = s.split(" ");
            String pre = ss[0];
            String in = ss[1];
            // 构建⼆叉树
            TreeNode res = afterHelper(pre.toCharArray(), in.toCharArray());
            //打印⼆叉树
            printTree(res);
            System.out.println();
        }
    }
    public static void printTree(TreeNode root) {
        if (root == null) return;
        printTree(root.left);
        printTree(root.right);
        System.out.print(root.val);
    }
}
```

# 21. ⼀组多⾏数据，第⼀⾏为数字n,表示后⾯有n⾏，后⾯每⾏为1个字符加2个整数，输出树节点的后序遍历字符串

https://kamacoder.com/problempage.php?pid=1021

```java
import java.util.*;
class TreeNode {
    char val;
    TreeNode left;
    TreeNode right;
    public TreeNode(char val) {
        this.val = val;
    }
}
public class Main{
    static TreeNode[] nodes = new TreeNode[30];
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        while (sc.hasNextInt()) {
            int len = sc.nextInt();
            for (int i = 0; i < len; i++) {
                // 获取字符和左右⼦节点
                char val = sc.next().charAt(0);
                int left = sc.nextInt();
                int right = sc.nextInt();
            }
            preorder(nodes[1]);
            System.out.println();
            inorder(nodes[1]);
            System.out.println();
            postorder(nodes[1]);
            System.out.println();
        }
    }
    public static void preorder(TreeNode root) {
        if (root == null) return;
        System.out.print(root.val);
        preorder(root.left);
        preorder(root.right);
    }
    public static void inorder(TreeNode root) {
        if (root == null) return;
        inorder(root.left);
        System.out.print(root.val);
        inorder(root.right);
    }
    public static void postorder(TreeNode root) {
        if (root == null) return;
        postorder(root.left);
        postorder(root.right);
        System.out.print(root.val);
    }
}
```

# 22. 多组测试数据，⾸先给出正整数N，接着输⼊两⾏字符串，字符串⻓度为N

https://kamacoder.com/problempage.php?pid=1022

```java
// ⽅法⼀：递归
import java.util.Scanner;
public class Main {
    static class TreeNode {
        char val;
        TreeNode left;
        TreeNode right;
        TreeNode(char val) {
            this.val = val;
            this.left = null;
            this.right = null;
        }
    }
    private static int getHeight(TreeNode root) {
        if (root == null)
            return 0;
        int leftHeight = getHeight(root.left);
        int rightHeight = getHeight(root.right);
        return Math.max(leftHeight, rightHeight) + 1;
    }
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        while (sc.hasNext()) {
            sc.nextInt();
            String preOrder = sc.next();
            String inOrder = sc.next();
            TreeNode root = buildTree(preOrder, inOrder);
            int height = getHeight(root);
            System.out.println(height);
        }
        sc.close();
    }
}
```

# 23. 多组测试数据。每组输⼊占⼀⾏，为两个字符串，由若⼲个空格分隔

https://kamacoder.com/problempage.php?pid=1023

```java
import java.util.Scanner;
public class Main {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        while (scanner.hasNextLine()) {
            String line = scanner.nextLine();
            String[] s = line.split(" ");
            String x = s[0];
            String y = s[1];
            int m = x.length();
            int n = y.length();
            // 初始化dp数组
            int[][] dp = new int[m + 1][n + 1];
            // 输出
            int max = dp[m][n];
            System.out.println(max);
        }
    }
}
```

# 24. 多组测试数据，每组第⼀⾏为两个正整数n和m，接下来m⾏，每⾏3个整数, **最后⼀⾏两个整数**

https://kamacoder.com/problempage.php?pid=1024

```java
import java.util.Arrays;
import java.util.Scanner;
public class Main {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        while (scanner.hasNext()) {
            // 处理输⼊
            int n = scanner.nextInt();
            int m = scanner.nextInt();
            for (int i = 0; i < m; i++) {
                int a = scanner.nextInt();
                int b = scanner.nextInt();
                int l = scanner.nextInt();
            }
            int x = scanner.nextInt();
            int y = scanner.nextInt();
            // 处理输出
            int res = dfs(graph, x, y, isVisit, sum);
            if (res != Integer.MAX_VALUE) {
                System.out.println(res);
            } else {
                System.out.println("No path");
            }
        }
    }
    private static int dfs(int[][] graph, int start, int end, int[] isVisit, int sum) {
        if (end == start) {
            return sum;
        }
        return min;
    }
}
