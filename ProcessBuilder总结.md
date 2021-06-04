<!-- Ctrl/Cmd + B	Toggle bold
Ctrl/Cmd + I	Toggle italic
Ctrl/Cmd + Shift + ]	Toggle heading (uplevel)
Ctrl/Cmd + Shift + [	Toggle heading (downlevel)
Ctrl/Cmd + M	Toggle math environment
Alt + C	Check/Uncheck task list item
Ctrl/Cmd + Shift + V	Toggle preview
Ctrl/Cmd + K V	Toggle preview to side -->
# ProcessBuilder 总结

> java.lang.Runtime.exec()方法以及java.lang.ProcessBuilder.start()方法可以被用来调用外部程序,并使用java.lang.Proccess对象来表示该程序。 其中java.lang.Runtime.exec()的源代码使用的是ProcessBuilder, 而且ProcessBuilder的平台兼容性比Runtime.exec()好，因此调用外部程序，我们应该使用ProcessBuilder()

## 用法

```java
        ProcessBuilder pb = new ProcessBuilder(new String[0]);
        List<String> commandList = new ArrayList();
        commandList.add("cmd");
        commandList.add("/c"); // 执行完后续命令后，关闭命令行窗口
        // 具体命令
        String scanCommand = "java -jar SensitiveTool.jar startscan -project " + 
            projXmlPath +  " -reporttype excel -reportname " + cmcName + " -exportpath " + reportPath + " 1>NUL 2>NUL";
        // 输出重定向，防止外部程序因为外部缓冲区被填满而阻塞。
        String[] commands = scanCommand.split(" ");
        commandList.addAll(Arrays.asList(commands) );
        pb.command(commandList);
        pb.directory(new File(senInfoPath)); //设置运行路径
        pb.redirectErrorStream(true);
        try {
            Process process = pb.start();
//            InputStream is = process.getInputStream();
//            int c;
//            while ((c = is.read()) != -1)
//            {
//                System.out.print((char) c);
//            } // 获取外部输出，防止外部程序因为外部缓冲区被填满而阻塞。
            process.waitFor();
        } catch (IOException | InterruptedException e) {
            e.printStackTrace();
        }
```

## 等待外部进程结束

precess.watiFor()

## 注意事项

Proccess对象使用三种流对象来与外部的程序进行交互：一个OutputStream对象用来给外部程序提供输入，一个InputStream对象用来接收外部程序的输出，以及一个InputStream对象来接收外部程序的错误信息。一个外部进程可能需要输入信息（通过Proccess.getOutputStream()输入），也可能会输出信息或错误（通过Proccess.getInputStream()或者Proccess.getErrorStream()获取）。不正确的使用和处理外部进程的输入输出可能导致未知的异常、DoS以及其他安全问题。一个试图读取输入的外部进程会一直阻塞直到向其提供输入，调用这样的外部进程时必须对其提供输入。<font face=黑体 color=#FF6666 size=4>当一个外部进程通过其输出流对外输出信息或错误时，必须及时清空其输出流，以防止输出流中的缓冲区被耗尽而导致外部进程被阻塞。 </font>

**推荐用法：**
``` java
public class Exec{
    public static void main(String args[]) throws IOException,InterruptedException{
        ProcessBuilder pb = new ProcessBuilder("notemaker");

        pb = pb.redirectErrorStream(true);
        Process proc = pb.start();
        InputStream is = proc.getInputStream();
        int c;
        while ((c = is.read()) != -1){
            System.out.print((char) c);
        }
        int exitVal = proc.waitFor();
    }
}
```

