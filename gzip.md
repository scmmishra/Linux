## Intro

The "gzip" command is a common way of compressing files within Linux and therefore it is worth knowing how to compress files using this tool.
The compression method used by "gzip" is Lempel-Ziv (LZ77). Now it isn't vital you know this information. All you need to know is that the files get smaller when you compress them with the "gzip" command.

By default when you compress a file or folder using the "gzip" command it will have the same file name as it did before but now it will have the extension ".gz".

In some cases, it isn't possible to keep the same name especially if the file name is incredibly long. In these circumstances, it will try to truncate it.

## How To Compress A File Using "gzip"

The simplest way to compress a single file using gzip is to run the following command:

```
gzip filename
```

For instance to compress a file called "mydocument.odt" run the following command:

```
gzip mydocument.odt
```

The "gzip" command will only attempt to compress regular files and folders. Therefore if you try and compress a symbolic link it will not work and it really doesn't make sense to do so.

## How To Decompress A File Using The "gzip" Command

If you have a file that has already been compressing you can use the following command to decompress it.

```
gzip -d filename.gz
```

For instance, to decompress the "mydocument.odt.gz" file you would use the following command:

```
gzip -d mydocument.odt.gz
```

## Force A File To Be Compressed

Sometimes a file cannot be compressed. Perhaps you are trying to compress a file called "myfile1" but there is already a file called "myfile1.gz". In this instance, the "gzip" command won't ordinarily work.

To force the "gzip" command to do its stuff simply run the following command:

```
gzip -f filename
```

## How To Keep The Uncompressed File

By default when you compress a file using the "gzip" command you end up with a new file with the extension ".gz".

If you want to compress the file and keep the original file you have to run the following command:

```
gzip -k filename
```

For example, if you run the following command you would end up with a file called "mydocument.odt" and "mydocument.odt.gz".

```
gzip -k mydocument.odt
```

##Get Some Stats About How Much Space You Saved
The whole point of compressing files is about saving disk space or to reduce the size of a file prior to sending it over a network.

It would be good therefore to see how much space was saved when you use the "gzip" command.

The "gzip" command provides the kind of statistics you require when checking for compression performance.

To get the list of statistics run the following command:

```
gzip -l filename.gz
```

The information returned by the above command is as follows:

*   Compressed size
*   Uncompressed size
*   Ratio as a percentage
*   Uncompressed filename

## Compress Every File In A Folder And Subfolders

You can compress every file in a folder and its subfolders by using the following command:

```
gzip -r foldername
```

This doesn't create one file called foldername.gz. Instead, it traverses the directory structure and compresses each file in that folder structure.

If you want to compress the folder structure as one file you are better off creating a tar file and then gzipping the tar file as shown in this guide.

## How To Test The Validity Of A Compressed File

If you want to check that a file is valid, you can run the following command:

```
gzip -t filename
```

If the file is valid there will be no output.

## How To Change The Compression Level

You can compress a file in different ways. For instance, you can go for a smaller compression which will work faster or you can go for maximum compression which has the tradeoff of taking longer to run.

To get minimum compression at the fastest speed run the following command:

```
gzip -1 filename
```

To get maximum compression at the slowest speed run the following command:

```
gzip -9 filename
```

You can vary the speed and compression level by picking different numbers between 1 and 9.
