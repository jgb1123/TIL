# I/O

## 입출력에서의 스트림
* I/O에서의 스트림은 데이터를 전송하는 연결 통로를 말한다.
* 스트림은 단방향 통신만 가능하기 때문에 하나의 스트림으로 입력과 출력을 동시에 처리할 수 없다.
* 입력을 위한 스트림과, 출력을 위한 스트림 2개의 스트림이 필요하다.
* 먼저 보낸 데이터를 먼저 받게 되어있으며, 연속적으로 데이터를 주고받는다.
## 바이트 기반 스트림
* 스트림은 바이트 단위로 데이터를 전송하며, 입출력 대상에 맞는 입출력 스트림을 사용한다.

| 입력스트림                | 출력스트림                 | 입출력 대상의 종류      |
|----------------------|-----------------------|-----------------|
| FileInputStream      | FileOutputStream      | 파일              |
| ByteArrayInputStream | ByteArrayOutputStream | 메모리(byte 배열)    |
| PipedInputStream     | PipedOutputStream     | 프로세스(프로세스 간 통신) |
| AudioInputStream     | AudioOutputStream     | 오디오 장치          |
* 이 스트림들은 InputStream과 OutputStream의 자손들이고, 읽고 쓰는데 필요한 추상메서드를 각각에 맞게 구현했다.


## 바이트 기반 보조 스트림
* 스트림의 기능을 보완하기 위해 제공된 스트림이다.
* 실제 데이터를 주고받는 스트림은 아니지만, 스트림의 기능을 향상시키거나 새로운 기능을 추가할 수 있다.
* 보조 스트림은 일반 스트림을 생성 한 다음 사용해야 한다.

| 입력                                | 출력                   | 설명                                                                    |
|-----------------------------------|----------------------|-----------------------------------------------------------------------|
| FilterInputStream                 | FilterOutputStream   | 필터를 이용해 입출력을 처리한다.                                                    |
| BufferedInputStream               | BufferedOutputStream | 버퍼를 이용해 입출력 성능을 향상 시킨다.                                               |
| DataInputStream                   | DataOutputStream     | int, float과 같은 기본형 단위로 데이터를 처리한다.                                     |
| SequenceInputStream               | -                    | 두개의 스트림을 하나로 연결한다.                                                    |
| LineNumberInputStream(deprecated) | -                    | 읽어온 데이터의 라인 번호를 카운트한다.<br/>JDK 1.1부터 문자기반 스트림인 LineNumberReader 사용 권장 |
| ObjectInputStream                 | ObjectOutputStream   | 데이터를 객체 단위로 읽고 쓰는데 사용한다.                                              |
| -                                 | PrintStream          | 버퍼를 이용하여 추가적인 print관련 기능이다(print, printf, println)                    |
| PushbackInputStream               | -                    | 버퍼를 이용해서 읽어 온 데이터를 다시 되돌리는 기능이다.                                      |
* 버퍼를 사용한 입출력과 사용하지 않은 입출력간의 성능차이는 상당하기 때문에 대부분 버퍼를 사용한 보조스트림을 이용한다.
* 모든 바이트기반 보조 스트림은 InputStream과 OutputStream의 자손들이고, 입출력 방식이 동일하다.

## 문자기반 스트림
* 바이트기반 스트림은 입출력 단위가 1byte가 된다.
* 자바는 한 문자를 의미하는 char형이 1byte가 아닌 2byte이기 때문에 바이트기반 스트림으로는 2byte문자를 처리하는데 어려움이 있다.
* 이러한 문제를 보완할 수 있는게 문자기반 스트림이다.
* 사용 목적과 방식은 바이트기반 스트림과 동일하다.

| 바이트기반 스트림	                                     | 문자기반 스트림                            |
|------------------------------------------------|-------------------------------------|
| FileInputStream<br/>FileOutputStream           | FileReader<br/>FileWriter           |
| ByteArrayInputStream<br/>ByteArrayOutputStream | CharArrayReader<br/>CharArrayWriter |
| PipedInputStream<br/>PipedOutputStream         | PipedReader<br/>PipedWriter         |
* 이 스트림들은 Reader와 Writer의 자손들이다.

## 문자기반 보조 스트림
* 보조스트림 역시 문자 기반 보조스트림이 존재한다.
* 사용 목적과 방식은 바이트기반 보조스트림과 동일하다.
| 바이트기반 보조 스트림                                 | 문자기반 보조 스트림                       |
|----------------------------------------------|-----------------------------------|
| BufferedInputStream<br/>BufferedOutputStream | BufferedReader<br/>BufferedWriter |
| FilterInputStream<br/>FilterOutputStream     | FilterReader<br/>FilterWriter     |
| LineNumberInputStream(deprecated)            | LineNumberReader                  |
| PrintStream                                  | PrintWriter                       |
| PushbackInputStream                          | PushbackReader                    |
* 모든 문자기반 보조 스트림은 Reader와 Wirter의 자손들이다.

## InputStream과 OutputStream
* 자바에서는 java.io 패키지를 통해서 많은 종류의 입출력 관련 클래스들을 제공하고 있다.
* InputStream과 OutputStream은 모든 바이트기반 스트림의 조상이다.
* 입출력의 대상이 달라져도 동일한 방법으로 출력할 수 있다.
### InputStream 메서드
| 메서드명                                 | 설명                                                                                                               |
|--------------------------------------|------------------------------------------------------------------------------------------------------------------|
| int avaliable()                      | 입력스트림으로부터 읽어올 수 있는 데이터의 크기를 반환한다.                                                                                |
| void close()                         | 입력스트림을 닫음으로써 사용하고 있던 자원을 반환한다.                                                                                   |
| void mark(int readlimit)             | 현재 위치를 표시해 놓는다. 후에 reset()에 의해서 표시해 놓은 위치로 다시 돌아갈 수 있다.                                                          |
| void reset()                         | 스트림에서 위치를 마지막으로 mark()가 호출되었던 위치로 돌아간다.                                                                          |
| boolean markSupported()              | mark()와 reset()을 지원하는지를 알려준다. 이 메서드들은 지원하는 것은 선택적 이므로, 사용하기 전에 markSupported()를 호출해서 지원여부를 확인해야 한다.              |
| abstract int read()                  | 1byte를 읽어 온다.(0~255 사이의 값) 더 이상 읽어 올 데이터가 없으면 -1을 반환한다.<br/>abstract메서드이기 때문에 InputStream의 자손들이 상황에 알맞게 구현해야 한다. |
| int read(byte[] b)                   | 배열의 b의 크기만큼 읽어서 배열을 채우고, 읽어 온 데이터 수를 반환한다.<br/>반환하는 값은 항상 배열의 크기보다 작거나 같다.                                       |
| int read(byte[] b, int off, int len) | 최대 len개의 byte를 읽어서 배열 b의 지정된 위치(off)부터 저장한다.<br/>실제로 읽어올 수 있는 데이터가 len개보다 적을 수 있다.                               |
| long skip(long n)                    | 스트림에서 주어진 길이(n)만큼 건너뛴다.                                                                                          |

* 스트림의 종류에 따라서 mark()와 reset()을 사용하여 이미 읽은 데이터를 되돌려서 다시 읽을 수 있다.
* 이러한 기능들을 지원하는지 확인하기 위해 markSupported()를 사용한다.
* 
### OutputStream 메서드
| 메서드명                                   | 설명                                                 |
|----------------------------------------|----------------------------------------------------|
| void close()                           | 입력소스를 닫음으로써 사용하고 있던 자원을 반환한다.                      |
| void flush()                           | 스트림 버퍼에 있는 모든 내용을 출력소스에 쓴다.                        |
| abstract void write(int b)             | 주어진 값을 출력소스에 쓴다.                                   |
| void write(byte[] b)                   | 주어진 배열 b에 저장된 모든 내용을 출력소스에 쓴다.                     |
| void write(byte[] b, int off, int len) | 주어진 배열 b에 저장된 내용 중에서 off번째부터 len개 만큼 읽어서 출력소스에 쓴다. |
* flush()는 버퍼가 있는 출력스트림의 경우에만 의미가 있다.
* 프로그램이 종료될 때 사용하고 닫지 않은 스트림을 JVM이 자동적으로 닫아주지만, 모든 작업을 마치고 난 후에는 close()를 호출해서 반드시 닫아주어야 한다.
* System.in System.out과 같은 표준 입출력 스트림과 메모리를 사용하는 스트림은(ByteArrayInputStream) 닫아주지 않아도 된다.

## Reader와 Writer
* 모든 바이트기반 스트림의 조상이 InputStream/OutputStream인 것과 같이, 모든 문자기반의 스트림에서의 조상은 Reader/Writer이다.
* Reader/Writer는 byte배열 대신 char배열을 사용한다는 것 외에는 InputStream/OutputStream의 메서드들과 크게 다르지 않다.
* Reader/Writer 그리고 그 자손들은 여러 종류의 인코딩과 자바에서 사용하는 유니코드(UTF-16)간의 변환을 자동적으로 처리해준다.
* Reader는 특정 인코딩을 읽어서 유니코드로 변환하고, Writer는 유니코드를 특정 인코딩으로 변환하여 저장한다.

### Reader 메서드

| 메서드명                                 | 설명                                                               |
|--------------------------------------|------------------------------------------------------------------|
| void close()                         | 스트림을 닫음으로써 사용하고 있던 자원을 반환한다.                                     |
| void mark(int readlimit)             | 현재 위치를 표시해 놓는다. 후에 reset()에 의해서 표시해 놓은 위치로 다시 돌아갈 수 있다.          |
| void reset()                         | 스트림에서 위치를 마지막으로 mark()가 호출되었던 위치로 돌아간다.                          |
| boolean markSupported()              | mark()와 reset()을 지원하는지를 알려준다.                                    |
| abstract int read()                  | 하나의 문자를 읽어 온다.(0~65535 사이의 값) 더 이상 읽어 올 데이터가 없으면 -1을 반환한다.       |
| int read(char[] c)                   | 배열 c의 크기만큼 읽어서 배열 c에 저장한다. 읽어 온 데이터 수 또는 -1을 반환한다.               |
| int read(char[] c, int off, int len) | 최대 len개의 char를 읽어서 배열 c의 지정된 위치(off)부터 저장한다. 읽어온 개수 또는 -1을 반환한다. |
| int read(CharBuffer target)          | 입력소스로부터 읽어와서 문자버퍼(target)에 저장한다.                                 |
| boolean ready()                      | 입력소스로부터 데이터를 읽을 준비가 되어있는지 알려준다.                                  |
| long skip(long n)                    | 현재 위치에서 주어진 문자 수(n)만큼 건너뛴다.                                      |

### Writer 메서드

| 메서드명                                              | 설명                                                     |
|---------------------------------------------------|--------------------------------------------------------|
| Writer append(char c)                             | 지정된 문자를 출력소스에 출력한다.                                    |
| Writer append(CharSequence c)                     | 지정된 문자열(CharSequence)을 출력소스에 출력한다.                     |
| Writer append(CharSequence c, int start, int end) | 지정된 문자열(CharSequence)의 일부를 출력소스에 출력한다                  |
| abstract void close()                             | 입력소스를 닫음으로써 사용하고 있던 자원을 반환한다.                          |
| abstract void flush()                             | 스트림 버퍼에 있는 모든 내용을 출력소스에 쓴다.                            |
| void write(int b)                                 | 주어진 값을 출력소스에 쓴다.                                       |
| void write(char[] c)                              | 주어진 배열 c에 저장된 모든 내용을 출력소스에 쓴다.                         |
| abstract void write(char[] c, int off, int len)   | 주어진 배열 c에 저장된 내용 중에서 off번째부터 len개 만큼 읽어서 출력소스에 쓴다.     |
| void write(String str)                            | 주어진 문자열(str)을 출력소스에 쓴다.                                |
| void write(String str, int off, int len)          | 주어진 문자열(str)의 일부를 출력소스에 쓴다. (off번째 문자열부터 len개 만큼의 문자열) |