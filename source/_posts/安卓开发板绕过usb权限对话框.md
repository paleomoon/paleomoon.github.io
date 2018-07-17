---
title: 安卓开发板绕过usb权限对话框
date: 2018-07-17 13:02:49
tags: [安卓]
categories: 安卓
---

当我们使用一些usb设备（如usb读卡器）连接到安卓开发板上时，往往会弹出询问usb权限的对话框，即使我们点击默认允许，系统重启后仍然会弹出对话框。很多时候希望板子启动后直接启动程序工作，就需要去掉这个对话框。

<!-- more -->

一般有两种解决方法，第一种是修改系统源码，找到这部分系统源码，替换掉弹窗部分。这种方法最简单有效，但代价是需要重新编译烧录系统，成本有点高。我没有用这种方法，参考链接有相关博文。这里主要介绍第二种方法。

我们可以伪造一个假的广播，也就是提前封装好点击对话框的工作，直接让自己接收广播，就绕开了那个对话框。步骤如下：

1. 在工程中增加包名为android.hardware.usb的包，并且添加IUsbManager.java文件。文件内容如下：

   ```
   package android.hardware.usb;

   public interface IUsbManager extends android.os.IInterface
   {
   /** Local-side IPC implementation stub class. */
   public static abstract class Stub extends android.os.Binder implements android.hardware.usb.IUsbManager {
       private static final java.lang.String DESCRIPTOR = "android.hardware.usb.IUsbManager";

       /** Construct the stub at attach it to the interface. */
       public Stub()         {
           throw new RuntimeException( "Stub!" );
       }

       /**
        * Cast an IBinder object into an android.hardware.usb.IUsbManager
        * interface, generating a proxy if needed.
        */
       public static android.hardware.usb.IUsbManager asInterface( android.os.IBinder obj) {
           throw new RuntimeException( "Stub!" );
       }

       @Override
       public android.os.IBinder asBinder() {
           throw new RuntimeException( "Stub!" );
       }

       @Override
       public boolean onTransact(int code, android.os.Parcel data, android.os.Parcel reply, int flags) throws android.os.RemoteException {
           throw new RuntimeException( "Stub!" );
       }

       static final int TRANSACTION_getDeviceList = (android.os.IBinder.FIRST_CALL_TRANSACTION + 0);
       static final int TRANSACTION_openDevice = (android.os.IBinder.FIRST_CALL_TRANSACTION + 1);
       static final int TRANSACTION_getCurrentAccessory = (android.os.IBinder.FIRST_CALL_TRANSACTION + 2);
       static final int TRANSACTION_openAccessory = (android.os.IBinder.FIRST_CALL_TRANSACTION + 3);
       static final int TRANSACTION_setDevicePackage = (android.os.IBinder.FIRST_CALL_TRANSACTION + 4);
       static final int TRANSACTION_setAccessoryPackage = (android.os.IBinder.FIRST_CALL_TRANSACTION + 5);
       static final int TRANSACTION_hasDevicePermission = (android.os.IBinder.FIRST_CALL_TRANSACTION + 6);
       static final int TRANSACTION_hasAccessoryPermission = (android.os.IBinder.FIRST_CALL_TRANSACTION + 7);
       static final int TRANSACTION_requestDevicePermission = (android.os.IBinder.FIRST_CALL_TRANSACTION + 8);
       static final int TRANSACTION_requestAccessoryPermission = (android.os.IBinder.FIRST_CALL_TRANSACTION + 9);
       static final int TRANSACTION_grantDevicePermission = (android.os.IBinder.FIRST_CALL_TRANSACTION + 10);
       static final int TRANSACTION_grantAccessoryPermission = (android.os.IBinder.FIRST_CALL_TRANSACTION + 11);
       static final int TRANSACTION_hasDefaults = (android.os.IBinder.FIRST_CALL_TRANSACTION + 12);
       static final int TRANSACTION_clearDefaults = (android.os.IBinder.FIRST_CALL_TRANSACTION + 13);
       static final int TRANSACTION_setCurrentFunction = (android.os.IBinder.FIRST_CALL_TRANSACTION + 14);
       static final int TRANSACTION_setMassStorageBackingFile = (android.os.IBinder.FIRST_CALL_TRANSACTION + 15);
       static final int TRANSACTION_allowUsbDebugging = (android.os.IBinder.FIRST_CALL_TRANSACTION + 16);
       static final int TRANSACTION_denyUsbDebugging = (android.os.IBinder.FIRST_CALL_TRANSACTION + 17);
   }

   /* Returns a list of all currently attached USB devices */
   public void getDeviceList(android.os.Bundle devices)
           throws android.os.RemoteException;

   /*
    * Returns a file descriptor for communicating with the USB device. The
    * native fd can be passed to usb_device_new() in libusbhost.
    */
   public android.os.ParcelFileDescriptor openDevice(
           java.lang.String deviceName) throws android.os.RemoteException;

   /* Returns the currently attached USB accessory */
   public android.hardware.usb.UsbAccessory getCurrentAccessory()
           throws android.os.RemoteException;

   /*
    * Returns a file descriptor for communicating with the USB accessory. This
    * file descriptor can be used with standard Java file operations.
    */
   public android.os.ParcelFileDescriptor openAccessory(
           android.hardware.usb.UsbAccessory accessory)
           throws android.os.RemoteException;

   /*
    * Sets the default package for a USB device (or clears it if the package
    * name is null)
    */
   public void setDevicePackage(android.hardware.usb.UsbDevice device,
           java.lang.String packageName, int userId)
           throws android.os.RemoteException;

   /*
    * Sets the default package for a USB accessory (or clears it if the package
    * name is null)
    */
   public void setAccessoryPackage(
           android.hardware.usb.UsbAccessory accessory,
           java.lang.String packageName, int userId)
           throws android.os.RemoteException;

   /* Returns true if the caller has permission to access the device. */
   public boolean hasDevicePermission(android.hardware.usb.UsbDevice device)
           throws android.os.RemoteException;

   /* Returns true if the caller has permission to access the accessory. */
   public boolean hasAccessoryPermission(
           android.hardware.usb.UsbAccessory accessory)
           throws android.os.RemoteException;

   /*
    * Requests permission for the given package to access the device. Will
    * display a system dialog to query the user if permission had not already
    * been given.
    */
   public void requestDevicePermission(android.hardware.usb.UsbDevice device,
           java.lang.String packageName, android.app.PendingIntent pi)
           throws android.os.RemoteException;

   /*
    * Requests permission for the given package to access the accessory. Will
    * display a system dialog to query the user if permission had not already
    * been given. Result is returned via pi.
    */
   public void requestAccessoryPermission(
           android.hardware.usb.UsbAccessory accessory,
           java.lang.String packageName, android.app.PendingIntent pi)
           throws android.os.RemoteException;

   /* Grants permission for the given UID to access the device */
   public void grantDevicePermission(android.hardware.usb.UsbDevice device,
           int uid) throws android.os.RemoteException;

   /* Grants permission for the given UID to access the accessory */
   public void grantAccessoryPermission(
           android.hardware.usb.UsbAccessory accessory, int uid)
           throws android.os.RemoteException;

   /*
    * Returns true if the USB manager has default preferences or permissions
    * for the package
    */
   public boolean hasDefaults(java.lang.String packageName, int userId)
           throws android.os.RemoteException;

   /* Clears default preferences and permissions for the package */
   public void clearDefaults(java.lang.String packageName, int userId)
           throws android.os.RemoteException;

   /* Sets the current USB function. */
   public void setCurrentFunction(java.lang.String function,
           boolean makeDefault) throws android.os.RemoteException;

   /* Sets the file path for USB mass storage backing file. */
   public void setMassStorageBackingFile(java.lang.String path)
           throws android.os.RemoteException;

   /*
    * Allow USB debugging from the attached host. If alwaysAllow is true, add
    * the the public key to list of host keys that the user has approved.
    */
   public void allowUsbDebugging(boolean alwaysAllow,
           java.lang.String publicKey) throws android.os.RemoteException;

   /* Deny USB debugging from the attached host */
   public void denyUsbDebugging() throws android.os.RemoteException;
   }
   ```

2. 增加包名为android.os的包，并且添加ServiceManager.java文件。文件内容如下：

   ```
   package android.os;  
     
   import java.util.Map;  
     
   public final class ServiceManager {  
       public static IBinder getService(String name) {  
           throw new RuntimeException("Stub!");  
       }  
     
       /** 
        * Place a new @a service called @a name into the service manager. 
        *  
        * @param name 
        *            the name of the new service 
        * @param service 
        *            the service object 
        */  
       public static void addService(String name, IBinder service) {  
           throw new RuntimeException("Stub!");  
       }  
     
       /** 
        * Retrieve an existing service called @a name from the service manager. 
        * Non-blocking. 
        */  
       public static IBinder checkService(String name) {  
           throw new RuntimeException("Stub!");  
       }  
     
       public static String[] listServices() throws RemoteException {  
           throw new RuntimeException("Stub!");  
       }  
     
       /** 
        * This is only intended to be called when the process is first being 
        * brought up and bound by the activity manager. There is only one thread in 
        * the process at that time, so no locking is done. 
        *  
        * @param cache 
        *            the cache of service references 
        * @hide 
        */  
       public static void initServiceCache(Map<String, IBinder> cache) {  
           throw new RuntimeException("Stub!");  
       }  
   }  
   ```

3. 新建一个广播接收类，内容如下：

   ```
   import java.util.HashMap;
   import java.util.Iterator;

   import android.content.BroadcastReceiver;
   import android.content.Context;
   import android.content.Intent;
   import android.content.pm.ApplicationInfo;
   import android.content.pm.PackageManager;
   import android.hardware.usb.IUsbManager;
   import android.hardware.usb.UsbDevice;
   import android.hardware.usb.UsbManager;
   import android.os.IBinder;
   import android.os.ServiceManager;

   public class LaunchReceiver extends BroadcastReceiver
   {
       @SuppressLint("NewApi")
   	public void onReceive( Context context, Intent intent )
       {
           String action = intent.getAction();
           if( action != null && action.equals( Intent.ACTION_BOOT_COMPLETED ) )
           {
               try
               {
                   PackageManager pm = context.getPackageManager();
                   ApplicationInfo ai = pm.getApplicationInfo( YOUR_APP_PACKAGE_NAMESPACE, 0 );
                   if( ai != null )
                   {
                       UsbManager manager = (UsbManager) context.getSystemService( Context.USB_SERVICE );
                       IBinder b = ServiceManager.getService( Context.USB_SERVICE );
                       IUsbManager service = IUsbManager.Stub.asInterface( b );

                       HashMap<String, UsbDevice> deviceList = manager.getDeviceList();
                       Iterator<UsbDevice> deviceIterator = deviceList.values().iterator();
                       while( deviceIterator.hasNext() )
                       {
                               UsbDevice device = deviceIterator.next();
                               if( device.getVendorId() == 0x4292 )//usb设备的vid（厂家提供）
                               {
                                   service.grantDevicePermission( device, ai.uid );
                                   service.setDevicePackage( device, YOUR_APP_PACKAGE_NAMESPACE ,ai.uid);
                               }
                       }
                   }
               }
               catch( Exception e )
               {
                   //trace( e.toString() );
               }
           }
       }
   }
   ```

4. 将应用拷贝在/system/app下面，点击安装，成为系统应用。将APK拷贝到/system/app的方法是：

   ```
   #adb shell  
   # mount -o remount,rw -t ext4 /dev/block/mtdblock3 /system // 让分区可写。  
   //windows回到命令提示符下  
   >adb push xxx.apk /system/app/
   ```

5. 当然，不要忘了在AndroidManifest.xml文件中添加权限：

   ```
   <uses-permission android:name="android.permission.MANAGE_USB" />
   ```

   ```
   <intent-filter>
        <action android:name="android.hardware.usb.action.USB_DEVICE_ATTACHED" />
   </intent-filter>
   <meta-data android:name="android.hardware.usb.action.USB_DEVICE_ATTACHED"  
   	android:resource="@xml/device_filter"/>
   ```

   其中device_filter.xml文件包含usb读卡器的vid和pid:

   ```
   <?xml version="1.0" encoding="utf-8"?>
   <resources>
       <!-- 以下内容的 vendor-id、product-id就是USB的vid和pid了-->
       <usb-device vendor-id="4292" product-id="1234"/>   
   </resources>
   ```

   在步骤3中的广播接受类中可以指定此vid。

至此，我们可以不修改源码，在应用层绕过usb对话框。经测试，在安卓5.1.1版本成功！如果其他版本的系统无效，可能需要根据对应版本的系统源码，修改步骤1、2的代码。

参考

- [bypass android usb host permission confirmation dialog](https://stackoverflow.com/questions/13726583/bypass-android-usb-host-permission-confirmation-dialog)
- [绕过android usb主机permision确认对话框](https://blog.csdn.net/mlj1668956679/article/details/14122787)
- [Android绕过usb主机permision确认对话框framework修改方案](https://blog.csdn.net/u010312937/article/details/62045812)
- [Android定制：去掉USB权限弹窗](https://blog.csdn.net/godiors_163/article/details/72526792)

