---
title: stream相关操作
date: 2022-03-31T07:48:28.781Z
---
```java
public class streams {

    class Student{
        private int id;
        private String name;
        private int age;
        public Student(int id,String name,int age){
            this.id = id;
            this.name = name;
            this.age = age;
        }
        public int getId() {
            return id;
        }
        public String getName() {
            return name;
        }
        @Override
        public String toString() {
            return "Student{" +
                    "id=" + id +
                    ", name='" + name + '\'' +
                    ", age=" + age +
                    '}';
        }
    }


    public static void main(String[] args) {
        streams streamss = new streams();

        List<Student> list = new ArrayList<>();
        list.add(streamss.new Student(1, "张三", 18));
        list.add(streamss.new Student(2, "李四", 20));
        list.add(streamss.new Student(3, "王二", 22));
        list.add(streamss.new Student(4, "小明", 19));
        list.add(streamss.new Student(5, "小红", 17));
        list.add(streamss.new Student(3, "王二", 22));
        list.add(null);


        /**
         * 过滤为null的项
         */
        // list.stream ().filter (Objects::nonNull).forEach (student -> System.out.println (student));
        /**
         * 对象的某个属性--成列表
         */
        // List<String> nameList = list.stream().filter(Objects::nonNull).map(Student::getName).collect(Collectors.toList());
        // nameList.forEach(System.out::println);
        /**
         * 取元素 成 列表 （去重）
         */
        // List<Integer> lista = list.stream().filter(Objects::nonNull).map(Student::getId).distinct().collect(Collectors.toList());
        // lista.forEach(System.out::println);
        /**
         * limit截取前n条
         */
        // List<String> nameList2 = list.stream ().filter (Objects::nonNull).map (Student::getName).limit (3).collect(Collectors.toList());
        // nameList2.forEach (s -> System.out.println ("limit" + s));
        /**
         * skip跳过  +  limie截取
         */
        // List<String> nameList3 = list.stream ().filter (Objects::nonNull).map (Student::getName).skip (1).limit (3).collect(Collectors.toList());
        // nameList3.forEach (s -> System.out.println ("skip  limit" + s));
        /**
         * 改变原有元素
         */
        // List<Integer> list2 = Arrays.asList(1,2,3);
        // list2.stream().map(i -> i * 2).forEach(integer -> System.out.println ("改变原有元素"+integer));

        // Map<Integer,Student> map = list.stream ().filter (Objects::nonNull).collect (Collectors.toMap (Student::getId,student -> student));
        // for (Map.Entry<Integer, streams.Student> entrySet: map.entrySet ()){
        //     System.out.println (entrySet.getKey () +":"+ entrySet.getValue ());
        // }
        /**
         * list --- >   map   （去重）
         */
        // Map<Integer, Student> collect1 = list.stream().filter(Objects::nonNull).collect(Collectors.toMap(Student::getId, Function.identity(), (a, b) -> a));
        // System.out.println(collect1);
        /**
         * list --- >   map （去重）
         */
        // Map<Integer, List<Student>> collect = list.stream().filter(Objects::nonNull).collect(Collectors.groupingBy(Student::getId));
        // System.out.println(collect);
        /**
         * 根据元素值排序
         */
        // List<Student> notNullList = list.stream ().filter (Objects::nonNull).collect (Collectors.toList ());
        // Collections.sort (notNullList,Comparator.comparing (o -> o.age));
        // notNullList.forEach (student -> System.out.println (student));
        /**
         * 并行流  乱序  正序
         */
        // List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9);
        // numbers.parallelStream().forEach(System.out::println);
        // numbers.parallelStream ().forEachOrdered (System.out::println);
        /**
         * 整体去重
         */
        // list = list.stream().filter(Objects::nonNull).filter(distinctByKey(Student::getId)).collect(Collectors.toList());
        // System.out.println(list);

        /**
         * 整体去重
         */
        ArrayList<Student> collect = list.stream().filter(Objects::nonNull).collect(Collectors.collectingAndThen(Collectors.toCollection(() -> new TreeSet<>(Comparator.comparing(Student::getId))), ArrayList::new));
        System.out.println(collect);
    }

    private static <T> Predicate<T> distinctByKey(Function<? super T, Object> keyExtractor) {
        Map<Object, Boolean> seen = new ConcurrentHashMap<>();
        return t -> seen.putIfAbsent(keyExtractor.apply(t), Boolean.TRUE) == null;
    }
}
```