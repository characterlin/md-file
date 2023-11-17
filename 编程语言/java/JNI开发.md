### 生成native header文件

- javah ---JDK8 生成头文件

- javac -h  JDK10开始 用于生成头文件











参考文章

[Java Native Interface (JNI) - Java Programming Tutorial (chua.bitbucket.io)](https://chua.bitbucket.io/java/JavaNativeInterface.html)

https://www.baeldung.com/jni#2-using-objects-and-calling-java-methods-from-native-code







If you're going to be doing this with a lot of objects, something like Swig would be best. You could use jobject type to pass around custom objects. The syntax isn't nice, perhaps there is a better way to write this.

Example Employee object:

```csharp
public class Employee {
    private int age;

    public Employee(int age) {
        this.age = age;
    }

    public int getAge() {
        return age;
    }
}
```

Call this code from some client:

```java
public class Client {
    public Client() {
        Employee emp = new Employee(32);

        System.out.println("Pass employee to C and get age back: "+getAgeC(emp));

        Employee emp2 = createWithAge(23);

        System.out.println("Get employee object from C: "+emp2.getAge());
    }

    public native int getAgeC(Employee emp);
    public native Employee createWithAge(int age);
}
```

#### prase Object

You could have JNI functions like this for passing an employee object from Java to C, as a jobject method argument:

```c
JNIEXPORT jint JNICALL Java_Client_getAgeC(JNIEnv *env, jobject callingObject, jobject employeeObject) {
    jclass employeeClass = (*env)->GetObjectClass(env, employeeObject);
    jmethodID midGetAge = (*env)->GetMethodID(env, employeeClass, "getAge", "()I");
    int age =  (*env)->CallIntMethod(env, employeeObject, midGetAge);
    return age;
}
```

#### constructor object

Passing an employee object back from C to Java as a jobject, you could use:

```c
JNIEXPORT jobject JNICALL Java_Client_createWithAge(JNIEnv *env, jobject callingObject, jint age) {
    jclass employeeClass = (*env)->FindClass(env,"LEmployee;");
    jmethodID midConstructor = (*env)->GetMethodID(env, employeeClass, "<init>", "(I)V");
    jobject employeeObject = (*env)->NewObject(env, employeeClass, midConstructor, age);
    return employeeObject;
}
```