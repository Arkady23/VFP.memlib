# VFP.memlib — memlib.net.dll
### Оглавление
[Назначение](#Назначение)  
[Регистрация COM-сервера в реестре Windows](#Регистрация-COM-сервера-в-реестре-Windows)  
[Создание объекта VFP.memlib](#Создание-объекта-VFPmemlib)  
[Объект Stream](#Объект-Stream)  
&emsp; [Write(str)](#Writestr)  
&emsp; [LenStream()](#LenStream)  
&emsp; [Asc()](#Asc)  
&emsp; [Read(count)](#Readcount)  
&emsp; [ReadLine()](#ReadLine)  
&emsp; [ReadToEnd()](#ReadToEnd)  
&emsp; [CloseStream()](#CloseStream)  
[Объект Array](#Объект-Array)  
&emsp; [NewArray(n)](#newArrayn)  
&emsp; [LenArray()](#LenArray)  
&emsp; [PutArray(i, val)](#PutArrayi-val)  
&emsp; [GetArray(i)](#GetArrayi)  
&emsp; [CloseArray()](#CloseArray)  
[Объект Dictionary](#Объект-Dictionary)  
&emsp; [LenDic()](#LenDic)  
&emsp; [PutDic(strKey, val)](#PutDicstrKey-val)  
&emsp; [GetDic(strKey)](#GetDicstrKey)  
&emsp; [DelDic(strKey)](#DelDicstrKey)  
&emsp; [СloseDic()](#СloseDic)  
[История версий](#История-версий)  
### Назначение
Библиотека memlib.net.dll реализует COM-сервер для VFP9 или VFPA, ктоторый в принципе может использоваться и в любых других языках, поддерживающих COM технологию обмена данными.  

Microsoft VFP имеет ряд ограничений, связанных с использованием ОП. Теоретически в VFP cуществуют способы использовать ОП до 2 Гб, но при этом возникают очень большие утечки памяти, приводящие к блокировке работы VFP.  

При создании COM-объекта VFP.memlib, выделяемая ОП не занимает место в области VFP. Эта память выделяется на объекте VFP.memlib.  

В memlib.net.dll первой версии реализованы:
- объект Stream, который по используемым методам напоминает работу с файлом, находящимся в ОП;
- объект Array, представляющий одномерный массив с числовым индексом;
- объект Dictionary, представляющий одномерный массив с текстовым индексом (словарь).  

В будущем возможно будут добавлены и другие вспомогательные элементы, если в этом будет нуждаться сообщество программистов на VFP.  
### Регистрация COM-сервера в реестре Windows
Чтобы объект VFP.memlib был доступен в разрабатываемых программах, его нужно зарегистрировать в ОС с помощью утилиты regasm.exe. Например:
```
C:\Windows\Microsoft.NET\Framework64\v4.0.30319\regasm.exe D:\VFP\VFPA\memlib.net.dll /codebase
```
Предварительно поместите файл memlib.net.dll в удобную для вас папку, например, в папку, где находятся другие библиотеки Microsoft VFP.  
Для удаления регистрации из реестра Windows используйте ключ /unregister. Например:
```
C:\Windows\Microsoft.NET\Framework64\v4.0.30319\regasm.exe D:\VFP\VFPA\memlib.net.dll /unregister
```
Для выполнения вышеуказанных команд требуются права администратора.
## Создание объекта VFP.memlib
Текст кода на VFP:
```
oMem = CreateObject('VFP.memlib')
```
## Объект Stream
Объект Stream имеет ниже следующие методы.
### Write(str)
Метод записывает указанную параметром строку в объект Stream.
```
oMem.write("Привет, Мир!"+chr(10))
```
Если до выполнения метода Write(str) был сформирован указатель чтения при использовании методов Read(), ReadLine() или ReadToEnd(), то после исполнения Write(str) указатель чтения сбрасывается в начало потока Stream. Эту особенность можно использовать для сброса указателя чтения командой `oMem.write("")`. Указатель записи всегда находится в конце записанного потока.
### LenStream()
Метод подсчитывает количество записанных в объект Stream байт и возвращает 32-х разрядное положительное число или ноль, если запись в объект еще не производилась.
```
len = oMem.lenStream()
```
### Asc()
Метод читает код первого за указателем чтения символа, но не перемещает указаль.
```
kod = oMem.Asc()
```
Если поток пустой или достигнут конец потока, то метод возвращает значение -1.
### Read(count)
Метод читает заданное параметром количество символов из Stream и возвращает прочитанную строку. Указатель чтения переносится за последний прочитанный символ.
```
str = oMem.read(10)
```
Если начальная позиция указателя находилась в коце потока, то вместо строки возвращается значение NULL.
### ReadLine()
Метод читает из Stream строку до символа перевода строки. Символы CR и LF не включаются в возвращаемое методом значение. Указатель чтения переносится за последний прочитанный символ включая CR и LF.
```
str = oMem.readLine()
```
Если начальная позиция указателя находилась в коце потока, то вместо строки возвращается значение NULL.
### ReadToEnd()
Метод читает из Stream строку до конца потока. Указатель чтения переносится в конец.
```
str = oMem.readToEnd()
```
Если начальная позиция указателя находилась в коце потока, то вместо строки возвращается значение NULL.
### CloseStream()
Метод удаляет объект Stream из памяти. Память освобождается. Используйте этот метод, если вам больше не нужен объект Stream. Объект Stream создается при первом использовании метода Write(str).
```
oMem.closeStream()
```
## Объект Array
Объект Array имеет ниже следующие методы.
### NewArray(n)
Метод создает массив размером, передаваемым числовым параметром. В случае успешного создания массива метод возвращает число созданных элементов массива n. В противном случае возвращается ноль.
### LenArray()
Метод возвращает длинну массива.
### PutArray(i, val)
Метод присваивает элементу массива под номером первого числового параметра значение произвольного типа, переданное вторым параметром.
### GetArray(i)
Метод возвращает значение элемента массива с номером переданным числовым параметром.
### CloseArray()
Метод уничтожает массив.
## Объект Dictionary
Объект Dictionary имеет ниже следующие методы.
### LenDic()
Метод возвращает число пар ключ-значение.
### PutDic(strKey, val)
Метод присваивает элементу словаря со строковым ключем, заданным первым параметром, значение произвольного типа, переданное вторым параметром.
### GetDic(strKey)
Метод возвращает значение элемента словаря со строковым ключем, заданным параметром.
### DelDic(strKey)
Метод удаляет элемент словаря ключ-значение, заданное строковым параметром, содержащим значение ключа.
### СloseDic()
Метод уничтожает словарь и освобождает память.
### История версий
0.0.0.0. 08.03.2025. Опубликована первая рабочая версия с объектами Stream, Array и Dictionary.  
0.0.1.0. 09.03.2025. Добавлен метод CloseAll().  
