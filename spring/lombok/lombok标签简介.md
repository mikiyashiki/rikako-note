# lombok标签简介
## @NonNull
- 如果用@NonNull标签来修饰一个参数，在方法或构造器的开头会插入一个空校验来检查该参数是否为空。
- 如果将@NonNull标签用来修饰一个field，任何通过注解产生的方法（如@Setter产生的Setter）都会在试图分配该field一个值时进行空校验
## @RequiredArgsConstructor
被该注解修饰的类，会产生一个包含required args的构造器。required args包含final field和被特殊约束的field(如被@NonNull约束)
## @ToString
产生一个toString方法的实现，并且该实现会被所有对象继承
## @Data
@Data是一个快捷的注解，其将@ToString,@EqualsAndHashCode,@Getter/@Setter,@RequiredArgsConstructor等注解整合到了一起
- 对于@Data注解标注的类，如果类中包含一个方法，其方法名和@Data将要产生的方法相同并且参数个数也相同（不需要参数类型相同），那么该方法将不会被产生，并且不会产生任何警告或错误
- 对于@Data标注的类，如果该类显示声明了一个构造器，那么@Data不会再生成任何构造器
- 可以通过为@Data标注类中的方法添加@lombok.experimental.Tolerate来为lombok隐藏这些方法