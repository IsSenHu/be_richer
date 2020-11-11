> Type是Java编程语言中所有类型的公共高级接口（官方解释），也就是Java中所有类型的“爹”，它并不是我们平常工作中经常使用的int、String、List、Map等数据类型，而是从Java语言角度来说，对基本类型、引用类型向上的抽象；

#### Type体系中的类型包括：

- 原始类型（Type）：不仅仅包含我们平常所指的类，还包括枚举、数组、注解等；
- 参数化类型（ParameterizedType）：就是我们平常所用到的泛型List<String>、Map<K, V>、Class<?>；
- 数组类型（GenericArrayType）：并不是我们工作中所使用的数组String[]、byte[]，而是带泛型的数组，即T[]；
- 类型变量（TypeVariable）：比如T a；

> Type类几乎在各种框架中都能看到，尤其是涉及代理，反射的业务。理解好Type类也会对今后框架封装，源码解读有很大的好处。

#### 1. Type与子类、子接口的关系

![](https://studysssmd.oss-cn-chengdu.aliyuncs.com/java/%E6%88%AA%E5%B1%8F2020-09-27%20%E4%B8%8B%E5%8D%885.04.15.png)

#### 2. Type	

```java
public interface Type {
    
    default String getTypeName() {
        return toString();
    }
}
```

#### 3. ParameterizedType

```java
package java.lang.reflect;

public interface ParameterizedType extends Type {
    /**
     * Returns an array of {@code Type} objects representing the actual type
     * arguments to this type.
     *
     * <p>Note that in some cases, the returned array be empty. This can occur
     * if this type represents a non-parameterized type nested within
     * a parameterized type.
     *
     * @return an array of {@code Type} objects representing the actual type
     *     arguments to this type
     * @throws TypeNotPresentException if any of the
     *     actual type arguments refers to a non-existent type declaration
     * @throws MalformedParameterizedTypeException if any of the
     *     actual type parameters refer to a parameterized type that cannot
     *     be instantiated for any reason
     * @since 1.5
     */
    Type[] getActualTypeArguments();

    /**
     * Returns the {@code Type} object representing the class or interface
     * that declared this type.
     *
     * @return the {@code Type} object representing the class or interface
     *     that declared this type
     * @since 1.5
     */
    Type getRawType();

    /**
     * Returns a {@code Type} object representing the type that this type
     * is a member of.  For example, if this type is {@code O<T>.I<S>},
     * return a representation of {@code O<T>}.
     *
     * <p>If this type is a top-level type, {@code null} is returned.
     *
     * @return a {@code Type} object representing the type that
     *     this type is a member of. If this type is a top-level type,
     *     {@code null} is returned
     * @throws TypeNotPresentException if the owner type
     *     refers to a non-existent type declaration
     * @throws MalformedParameterizedTypeException if the owner type
     *     refers to a parameterized type that cannot be instantiated
     *     for any reason
     * @since 1.5
     */
    Type getOwnerType();
}
```

#### 4. TypeVariable

类型变量，即泛型中的变量；例如：T、K、V等变量，可以表示任何类；在这需要强调的是，TypeVariable代表泛型中的变量，而ParameterizedType代表整个泛型；

```java
package java.lang.reflect;

/**
 * TypeVariable is the common superinterface for type variables of kinds.
 * A type variable is created the first time it is needed by a reflective
 * method, as specified in this package.  If a type variable t is referenced
 * by a type (i.e, class, interface or annotation type) T, and T is declared
 * by the nth enclosing class of T (see JLS 8.1.2), then the creation of t
 * requires the resolution (see JVMS 5) of the ith enclosing class of T,
 * for i = 0 to n, inclusive. Creating a type variable must not cause the
 * creation of its bounds. Repeated creation of a type variable has no effect.
 *
 * <p>Multiple objects may be instantiated at run-time to
 * represent a given type variable. Even though a type variable is
 * created only once, this does not imply any requirement to cache
 * instances representing the type variable. However, all instances
 * representing a type variable must be equal() to each other.
 * As a consequence, users of type variables must not rely on the identity
 * of instances of classes implementing this interface.
 *
 * @param <D> the type of generic declaration that declared the
 * underlying type variable.
 *
 * @since 1.5
 */
public interface TypeVariable<D extends GenericDeclaration> extends Type, AnnotatedElement {
    /**
     * Returns an array of {@code Type} objects representing the
     * upper bound(s) of this type variable.  Note that if no upper bound is
     * explicitly declared, the upper bound is {@code Object}.
     *
     * <p>For each upper bound B: <ul> <li>if B is a parameterized
     * type or a type variable, it is created, (see {@link
     * java.lang.reflect.ParameterizedType ParameterizedType} for the
     * details of the creation process for parameterized types).
     * <li>Otherwise, B is resolved.  </ul>
     *
     * @throws TypeNotPresentException  if any of the
     *     bounds refers to a non-existent type declaration
     * @throws MalformedParameterizedTypeException if any of the
     *     bounds refer to a parameterized type that cannot be instantiated
     *     for any reason
     * @return an array of {@code Type}s representing the upper
     *     bound(s) of this type variable
    */
    Type[] getBounds();

    /**
     * Returns the {@code GenericDeclaration} object representing the
     * generic declaration declared this type variable.
     *
     * @return the generic declaration declared for this type variable.
     *
     * @since 1.5
     */
    D getGenericDeclaration();

    /**
     * Returns the name of this type variable, as it occurs in the source code.
     *
     * @return the name of this type variable, as it appears in the source code
     */
    String getName();

    /**
     * Returns an array of AnnotatedType objects that represent the use of
     * types to denote the upper bounds of the type parameter represented by
     * this TypeVariable. The order of the objects in the array corresponds to
     * the order of the bounds in the declaration of the type parameter.
     *
     * Returns an array of length 0 if the type parameter declares no bounds.
     *
     * @return an array of objects representing the upper bounds of the type variable
     * @since 1.8
     */
     AnnotatedType[] getAnnotatedBounds();
}
```

#### 5. GenericArrayType

泛型数组类型，用来描述ParameterizedType、TypeVariable类型的数组；即List<T>[] 、T[]等；

```java
package java.lang.reflect;

public interface GenericArrayType extends Type {
    /**
     * Returns a {@code Type} object representing the component type
     * of this array. This method creates the component type of the
     * array.  See the declaration of {@link
     * java.lang.reflect.ParameterizedType ParameterizedType} for the
     * semantics of the creation process for parameterized types and
     * see {@link java.lang.reflect.TypeVariable TypeVariable} for the
     * creation process for type variables.
     *
     * @return  a {@code Type} object representing the component type
     *     of this array
     * @throws TypeNotPresentException if the underlying array type's
     *     component type refers to a non-existent type declaration
     * @throws MalformedParameterizedTypeException if  the
     *     underlying array type's component type refers to a
     *     parameterized type that cannot be instantiated for any reason
     */
    Type getGenericComponentType();
}
```

#### 6. WildcardType

泛型表达式（或者通配符表达式），即? extends Number这样的表达式;WildcardType虽然是Type的子接口，但却不是Java类型中的一种：

```java
package java.lang.reflect;

public interface WildcardType extends Type {
    /**
     * Returns an array of {@code Type} objects representing the  upper
     * bound(s) of this type variable.  Note that if no upper bound is
     * explicitly declared, the upper bound is {@code Object}.
     *
     * <p>For each upper bound B :
     * <ul>
     *  <li>if B is a parameterized type or a type variable, it is created,
     *  (see {@link java.lang.reflect.ParameterizedType ParameterizedType}
     *  for the details of the creation process for parameterized types).
     *  <li>Otherwise, B is resolved.
     * </ul>
     *
     * @return an array of Types representing the upper bound(s) of this
     *     type variable
     * @throws TypeNotPresentException if any of the
     *     bounds refers to a non-existent type declaration
     * @throws MalformedParameterizedTypeException if any of the
     *     bounds refer to a parameterized type that cannot be instantiated
     *     for any reason
     */
    Type[] getUpperBounds();

    /**
     * Returns an array of {@code Type} objects representing the
     * lower bound(s) of this type variable.  Note that if no lower bound is
     * explicitly declared, the lower bound is the type of {@code null}.
     * In this case, a zero length array is returned.
     *
     * <p>For each lower bound B :
     * <ul>
     *   <li>if B is a parameterized type or a type variable, it is created,
     *  (see {@link java.lang.reflect.ParameterizedType ParameterizedType}
     *  for the details of the creation process for parameterized types).
     *   <li>Otherwise, B is resolved.
     * </ul>
     *
     * @return an array of Types representing the lower bound(s) of this
     *     type variable
     * @throws TypeNotPresentException if any of the
     *     bounds refers to a non-existent type declaration
     * @throws MalformedParameterizedTypeException if any of the
     *     bounds refer to a parameterized type that cannot be instantiated
     *     for any reason
     */
    Type[] getLowerBounds();
    // one or many? Up to language spec; currently only one, but this API
    // allows for generalization.
}
```

#### 7. 各种Type测试

##### 7.1 ParameterizedType

```java
package com.gapache.commons.type;

import java.lang.reflect.Field;
import java.lang.reflect.ParameterizedType;
import java.lang.reflect.Type;
import java.util.List;
import java.util.Map;
import java.util.Set;

/**
 * @author HuSen
 * @since 2020/9/27 3:17 下午
 */
public class ParameterizedTypeTest<T> {
    private static final String LINE = "----------" + System.lineSeparator();
    private List<T> list;
    private Set<T> set;
    private Map<String, T> map;
    private Map.Entry<String, Integer> map2;

    public static void main(String[] args) throws Exception {
        Field list = ParameterizedTypeTest.class.getDeclaredField("list");
        Type listGenericType = list.getGenericType();
        System.out.println("list域类型名：" + listGenericType.getTypeName());
        System.out.println("list域实际的Type类型：" + listGenericType.getClass().getName());
        System.out.println(LINE);

        Field set = ParameterizedTypeTest.class.getDeclaredField("set");
        Type setGenericType = set.getGenericType();
        System.out.println("set域类型名：" + setGenericType.getTypeName());
        System.out.println("set域实际的Type类型：" + setGenericType.getClass().getName());
        System.out.println(LINE);

        Field map = ParameterizedTypeTest.class.getDeclaredField("map");
        Type mapGenericType = map.getGenericType();
        System.out.println("map域类型名：" + mapGenericType.getTypeName());
        System.out.println("map域实际的Type类型：" + mapGenericType.getClass().getName());
        if (mapGenericType instanceof ParameterizedType) {
            ParameterizedType mapParameterizedType = (ParameterizedType) mapGenericType;
            Type[] actualTypeArguments = mapParameterizedType.getActualTypeArguments();
            System.out.println("map域泛型参数类型[0]:" + actualTypeArguments[0]);
            System.out.println("map域泛型参数类型[1]:" + actualTypeArguments[1]);
            System.out.println("map域声明泛型参数的类类型：" + mapParameterizedType.getRawType());
            System.out.println("泛型的拥有者类型：" + mapParameterizedType.getOwnerType());
        }
        System.out.println(LINE);

        Field map2 = ParameterizedTypeTest.class.getDeclaredField("map2");
        Type map2GenericType = map2.getGenericType();
        System.out.println("map2域类型名：" + map2GenericType.getTypeName());
        System.out.println("map2域实际的Type类型：" + map2GenericType.getClass().getName());
        if (map2GenericType instanceof ParameterizedType) {
            ParameterizedType map2ParameterizedType = (ParameterizedType) map2GenericType;
            Type[] types = map2ParameterizedType.getActualTypeArguments();
            System.out.println("map2域泛型参数类型[0]:" + types[0]);
            System.out.println("map2域泛型参数类型[1]:" + types[1]);
            System.out.println("map2域声明泛型参数的类类型：" + map2ParameterizedType.getRawType());
            System.out.println("泛型的拥有者类型：" + map2ParameterizedType.getOwnerType());
        }
    }
}
```

##### 7.2 GenericArrayType

```java
package com.gapache.commons.type;

import java.lang.reflect.Field;
import java.lang.reflect.GenericArrayType;
import java.lang.reflect.Type;
import java.util.List;

/**
 * @author HuSen
 * @since 2020/9/27 3:40 下午
 */
public class GenericArrayTypeTest<T> {
    private static final String LINE = "-------" + System.lineSeparator();
    private T[] t;
    private List<T>[] lists;

    public static void main(String[] args) throws Exception {
        Field lists = GenericArrayTypeTest.class.getDeclaredField("lists");
        Type listsGenericType = lists.getGenericType();
        System.out.println("lists域Type类型：" + listsGenericType.getClass().getName());
        GenericArrayType listsGenericArrayType = (GenericArrayType) listsGenericType;
        System.out.println("lists域元素类型:" + listsGenericArrayType.getGenericComponentType());
        System.out.println("lists域元素Type类型:" + listsGenericArrayType.getGenericComponentType().getClass().getName());
        System.out.println(LINE);

        Field t = GenericArrayTypeTest.class.getDeclaredField("t");
        Type tGenericType = t.getGenericType();
        System.out.println("t域Type类型：" + tGenericType.getClass().getName());
        GenericArrayType tGenericArrayType = (GenericArrayType) tGenericType;
        System.out.println("t域元素类型:" + tGenericArrayType.getGenericComponentType());
        System.out.println("t域元素Type类型:" + tGenericArrayType.getGenericComponentType().getClass().getName());
    }
}
```

##### 7.3 TypeVariable

```java
package com.gapache.commons.type;

import java.lang.reflect.Field;
import java.lang.reflect.GenericDeclaration;
import java.lang.reflect.Type;
import java.lang.reflect.TypeVariable;
import java.util.List;

/**
 * @author HuSen
 * @since 2020/9/27 4:12 下午
 */
public class TypeVariableTest<T> {
    private static final String LINE = "-------" + System.lineSeparator();
    private T a;
    private List<String> b;

    public static void main(String[] args) throws Exception {
        Field a = TypeVariableTest.class.getDeclaredField("a");
        Type aGenericType = a.getGenericType();
        Class<?> aType = a.getType();
        System.out.println("TypeVariableTest<T> a 域 type名：" + aGenericType.getTypeName());
        System.out.println("TypeVariableTest<T> a 域 type的class名：" + aGenericType.getClass().getCanonicalName());
        System.out.println("TypeVariableTest<T> a 域 Class名: " + aType.getCanonicalName());

        if (aGenericType instanceof TypeVariable) {
            TypeVariable aTypeVariable = (TypeVariable) aGenericType;
            String name = aTypeVariable.getName();
            Type[] bounds = aTypeVariable.getBounds();
            System.out.println("a 域类型是 TypeVariable类型！");
            System.out.println("a 域 type'name: " + name);
            for (int i = 0; i < bounds.length; i++) {
                System.out.println("a 域 type'bounds[" + i + "] Type'name: " + bounds[i].getTypeName());
            }
            GenericDeclaration genericDeclaration = aTypeVariable.getGenericDeclaration();
            System.out.println("声明a 域变量的实体：" + genericDeclaration);
        }
        System.out.println(LINE);

        Field b = TypeVariableTest.class.getDeclaredField("b");
        Type bGenericType = b.getGenericType();
        Class<?> bType = b.getType();
        System.out.println("TypeVariableTest<T> b 域 type名：" + bGenericType.getTypeName());
        System.out.println("TypeVariableTest<T> b 域 type的Class名：" + bGenericType.getClass().getCanonicalName());
        System.out.println("TypeVariableTest<T> b 域 Class名：" + bType.getCanonicalName());
    }
}
```

##### 7.4 WildcardType

```java
package com.gapache.commons.type;

import java.lang.reflect.Field;
import java.lang.reflect.ParameterizedType;
import java.lang.reflect.Type;
import java.lang.reflect.WildcardType;
import java.util.Arrays;
import java.util.List;

/**
 * @author HuSen
 * @since 2020/9/27 4:29 下午
 */
public class WildcardTypeTest {

    private List<? extends String> a;

    public static void main(String[] args) throws Exception {
        Field a = WildcardTypeTest.class.getDeclaredField("a");
        Type aGenericType = a.getGenericType();
        Class<?> aType = a.getType();

        System.out.println("TypeVariableTest a 域 type名：" + aGenericType.getTypeName());
        System.out.println("TypeVariableTest a 域 type的class名：" + aGenericType.getClass().getCanonicalName());
        System.out.println("TypeVariableTest a 域 Class名: " + aType.getCanonicalName());

        if (aGenericType instanceof ParameterizedType) {
            ParameterizedType aParameterizedType = (ParameterizedType) aGenericType;
            System.out.println("a 域Type 是 ParameterizedType ！");
            Type[] typesString = aParameterizedType.getActualTypeArguments();
            System.out.println("a 域泛型参数[0]的type名：" + typesString[0].getTypeName());
            System.out.println("a 域泛型参数[0]的type类型名：" + typesString[0].getClass().getCanonicalName());
            WildcardType type0 = (WildcardType) typesString[0];
            Type[] type0UpperBounds = type0.getUpperBounds();
            Type[] type0LowerBounds = type0.getLowerBounds();

            System.out.println("泛型参数[0]的上限type:" + type0UpperBounds[0]);
            System.out.println("泛型参数的下限type:" + Arrays.toString(type0LowerBounds));
        }
    }
}
```

