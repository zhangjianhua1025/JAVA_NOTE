## 1、Optional、OptionalDouble、OpticalInt、OptionalLong

------

​	A container object which may or may not contain a non-null value.

​	Additional methods that depend on the presence or absence of a contained value are provided.

​	use of identity-sensitive operations ，(including reference equality，identity hash code, or synchronization) on instances of Optional，may have 		        	unpredictable results and should be avoided.

### 1.1、Optional

------

 If a value is present in this {Optional}, returns the value,otherwise throws {NoSuchElementException}.

public T get()；