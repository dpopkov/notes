How to
======

Get the hard disk serial number
-------------------------------
This serial number is created by the OS where formatting the drive and it's not the manufacturer serial number.
```Java
for (FileStore store: FileSystems.getDefault().getFileStores()) {
    System.out.format("%-20s vsn:%s\n", store, store.getAttribute("volume:vsn"));
}
```
This solution is for Windows only.
