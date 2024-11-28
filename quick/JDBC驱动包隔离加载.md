
example:

```java
import java.io.File;
import java.lang.reflect.Field;
import java.net.MalformedURLException;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

import java.net.URL;
import java.net.URLClassLoader;
import java.sql.Connection;
import java.sql.Driver;
import java.sql.DriverManager;
import java.sql.DriverPropertyInfo;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.SQLFeatureNotSupportedException;
import java.sql.Statement;
import java.util.HashMap;
import java.util.Map;
import java.util.Properties;
import java.util.concurrent.CopyOnWriteArrayList;
import java.util.logging.Logger;



public class Main {

    public static void main(String[] args) throws SQLException, ClassNotFoundException, InstantiationException, IllegalAccessException, MalformedURLException {

        String jdbcUrl = "jdbc:mysql://127.0.0.1:3306/information_schema?useSSL=false&zeroDateTimeBehavior=convertToNull&allowMultiQueries=true";
        String username = "root";
        String password = "123456";
        Properties props = new Properties();
        props.put("user", username);
        props.put("password", password);

        String driverPath = "C:\\Users\\hongz\\Desktop\\projects\\cmiot5\\dsp_webapi\\database_connection\\src\\main\\resources\\libs\\mysql-connector-java-5.1.6.jar";
        String driverClass = "com.mysql.jdbc.Driver";

//        String driverPath = "C:\\Users\\hongz\\Desktop\\projects\\cmiot5\\dsp_webapi\\database_connection\\src\\main\\resources\\libs\\mysql-connector-j-8.3.0.jar";
//        String driverClass = "com.mysql.cj.jdbc.Driver";

        URL driverFileURL = new File(driverPath).toURI().toURL();
        URL[] fileURLs = new URL[] {driverFileURL};
        URLClassLoader urlClassLoader = new URLClassLoader(fileURLs, null);
        Driver driver = (Driver) urlClassLoader.loadClass(driverClass).newInstance();
        Connection connection = driver.connect(jdbcUrl, props);

//        Connection connection = DriverManager.getConnection(jdbcUrl,"root","123456");

        System.out.println("conn=" + connection);

       connection.close();
    }

    private static void closeUrlClassLoader(URLClassLoader classLoader) throws Exception {
        classLoader.close();
        classLoader.clearAssertionStatus();
        System.out.println("== finished clean url classloader");
    }
    // 清理DriverManager中的CopyOnWriteArrayList
    private static void cleanDriverManager() throws Exception {
        Field field = DriverManager.class.getDeclaredField("registeredDrivers");
        field.setAccessible(true);
        CopyOnWriteArrayList list = ((CopyOnWriteArrayList) field.get(null));
        for (Object obj : list) {
            list.remove(obj);
        }
        System.out.println("== finished clean driver manager list");
    }
}
```
