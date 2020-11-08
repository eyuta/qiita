# 【kotlin】日本語ファイルを ZIP 圧縮する

## はじめに

ZIP 圧縮時、つまり[ZipOutputStream](https://docs.oracle.com/javase/jp/8/docs/api/java/util/zip/ZipOutputStream.html#ZipOutputStream-java.io.OutputStream-) で使われる Charset のデフォルト値は UTF-8 となっています。

Windows の日本語ファイル名は MS932 なので、このままでは Windows で解凍した際に文字化けします。
そこで、ZipOutputStream で Charset を指定し、文字化け回避します。
似たような記事は Java ならたくさんありますが、Kotlin では少なかったので投下します。

尚、筆者は FileInputStream の使い方が分からないくらい Kotlin(Java) に慣れていないため、もっと良い書き方があればご教示いただけると嬉しいです。

## この記事で分かること

Kotlin を使用した ZIP 圧縮(日本語対応)のやり方。

※展開はやりません。

## 参考にさせていただいた記事様

- [Kotlin で zip を安全に圧縮・展開する](https://qiita.com/jim/items/2c0b0a0acacd78f49b49)
- [Java ZIP 圧縮する方法 ディレクトリ指定やファイル指定 - ZipEntry・ZipOutputStream](https://www.saka-en.com/java/java-zip-compress/)
- [Java ファイルコピー（バッファサイズを変更）](https://chat-messenger.com/blog/java/file-copy)
- [Shift_JIS と Windows-31J (MS932) の違いを整理してみよう](https://weblabo.oscasierra.net/shift_jis-windows31j/)
- [Java で SHIFT-JIS を見つけたら要注意](https://qiita.com/csharpisthebest/items/2cd61661dbc42d81aa45)

## コード

```kotlin
import java.io.BufferedOutputStream
import java.io.File
import java.io.FileInputStream
import java.io.FileOutputStream
import java.nio.charset.Charset
import java.util.zip.ZipEntry
import java.util.zip.ZipOutputStream

/**
 * 指定された ArrayList のファイルを ZIP アーカイブし、指定されたパスに作成します。
 * デフォルト文字コードは Shift_JIS ですので、日本語ファイル名も対応できます。
 *
 * @param fromFiles 圧縮するファイルリスト。  ( 例; {C:/sample1.txt, C:/sample2.txt} )
 * @param toZipFile 圧縮後のファイル名をフルパスで指定。 ( 例: C:/sample.zip )
 * @param charset  Zip化する際に使用するCharset。 ※1
 */
fun zipFiles(fromFiles: List<File>, toZipFile: File, charset: Charset = Charset.forName("MS932")) {
    try {
        // use を使うと、スコープが終わるタイミングで自動的にResourceをCloseしてくれます。
        // ZipOutputStreamの第1引数には、zip化するファイルを指定します。
        // ZipOutputStreamの第2引数が空の場合、CharsetがUTF-8となります。
        ZipOutputStream(BufferedOutputStream(FileOutputStream(toZipFile)), charset).use { zipOutputStream ->
            fromFiles.forEach { file ->
                if (file.isFile) {
                    archiveFile(zipOutputStream, file, file.name)
                } else if (file.isDirectory) {
                    archiveDirectory(zipOutputStream, file, file)
                }
            }
        }
    } catch (e: Exception) {
        // エラー処理。
    }
}

/**
 * 指定された ArrayList のファイルを ZIP アーカイブし、指定されたパスに作成します。
 * デフォルト文字コードは Shift_JIS ですので、日本語ファイル名も対応できます。
 *
 * @param zipOutputStream targetFileを書き込むStream。
 * @param baseFile 圧縮するディレクトリのルートファイル。
 * @param targetFile 圧縮するディレクトリ。
 */
fun archiveDirectory(zipOutputStream: ZipOutputStream, baseFile: File, targetFile: File) {
    targetFile.listFiles()?.forEach { file ->
        if (file.isDirectory) {
            archiveDirectory(zipOutputStream, baseFile, file)
        } else if (file.isFile) {
            // entryNameは相対パスで指定する必要があるため、baseFileとの相対パスを取得します。
            val relativeEntryName = file.toRelativeString(baseFile.parentFile)
            archiveFile(zipOutputStream, file, relativeEntryName)
        } else {
            // ファイルが存在しない場合、isDirectoryにもisFileにも該当しません。
            // その場合に処理を行いたい場合、ここに処理を書きます。
        }
    }
}

/**
 * 指定された ArrayList のファイルを ZIP アーカイブし、指定されたパスに作成します。
 * デフォルト文字コードは Shift_JIS ですので、日本語ファイル名も対応できます。
 *
 * @param zipOutputStream targetFileを書き込むStream。
 * @param targetFile 圧縮するファイル。
 * @param entryName targetFileをzipOutputStreamに書き込む際のファイル名(相対パス)。
 *          ex) hoge/fuga.txt であれば、 fizz.zip/hoge/fuga.txt にファイルが作られます。
 */
fun archiveFile(zipOutputStream: ZipOutputStream, targetFile: File, entryName: String) {
    // ※2
    val zipBufferSize = 100 * 1024
    // ${ZipOutputStreamで指定したFileのPath} / ${entryName} にファイルが作られます。
    zipOutputStream.putNextEntry(ZipEntry(entryName))
    FileInputStream(targetFile).use { inputStream ->
        val bytes = ByteArray(zipBufferSize)
        var length: Int
        // zipBufferSize分ファイルを読み込む。読み込み終わると -1 を返します。
        while (inputStream.read(bytes).also { length = it } != -1) {
            zipOutputStream.write(bytes, 0, length)
        }
    }
    // Entryは use で Closeされないため、手動でCloseする必要があります。
    zipOutputStream.closeEntry()
}

```

## Note

### ※1 使用する Charset について

今回、使用する Charset として MS932 を選択しています。
同じような文字コードである SHIFT_JIS ではなく MS932 を使用する理由は、以下の記事を参考にさせていただきました。

- [Shift_JIS と Windows-31J (MS932) の違いを整理してみよう](https://weblabo.oscasierra.net/shift_jis-windows31j/)
- [Java で SHIFT-JIS を見つけたら要注意](https://qiita.com/csharpisthebest/items/2cd61661dbc42d81aa45)

### ※2 FileInputStream で使用するバッファサイズについて

参考: [Java ファイルコピー（バッファサイズを変更）](https://chat-messenger.com/blog/java/file-copy)
![バッファサイズ](https://chat-messenger.com/images/file_copy_stream_reslt.jpg)

今回はアップロードされるファイルのサイズの想定が 1MB ~5MB 程度だったので、100KB を指定しました。
