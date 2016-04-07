# CRDEMO README                              
  
Contents of this $$README are contained in: ```<InstallHLQ>.CRDEMO.JCL($$README)```

Below is the list of files and instructions needed to restore the sample
VSAM KSDS & DB2 table for the Spark Customer Retention demo (CRDEMO):

These notes are intended for use by the z/OS and DB2 System Programmers!

## 1.) Download TRSBIN from Github
Download the following TRSBIN package from GitHub to your workstattion:

```
CRDEMO.DUMP.TRSBIN.20160406    <=== Current <GitHub_package_name>
```

## 2.) Upload TRSBIN package to an MVS dataset
In this section we will upload the TRSBIN package to an MVS dataset using FTP w/quote site
       
```
$ FTP <yourMVShost>
Userid:   <yourusername>
Password: <yourpassword>
$ bin
$ quote site RECFM=FB LRECL=1024 BLKSIZE=20480 CYL PRI=200 SEC=20
```

For SMS managed storage, use the following:

```
$ quote site STORCLAS=<SMS_STORCLAS> MGMTCLAS=<SMS_MGMTCLAS>
```

For Non-SMS managed storage, use the following:

```
$ quote site UNIT=3390 VOLUME=??????
```

Put the package into your user directory

```
$ cd '<userid>'
$ put <GitHub_package_name> CRDEMO.DUMP.TRSBIN
```

## 3.) Unterse the TRS Dataset
We will use the following JCL template to unterse the TRS dataset:

* Sample JCL located in ```<InstallHLQ>.CRDEMO.JCL(UNTRDEMO)```
* Change JOBCARD as appropriate for your system installation
* Change <userid> for your TSO userid on installation system
  * For SMS-managed target, use STORCLAS= DD card
  * For non SMS-managed target, use UNIT/VOL=SER= DD card
        
**UNTRDEMO JCL sample**

```
//UNTRDEMO JOB CLASS=A,MSGCLASS=H,NOTIFY=&SYSUID,REGION=0M
//UNTERSE EXEC PGM=TRSMAIN,PARM='UNPACK'
//SYSPRINT  DD SYSOUT=*,DCB=(LRECL=133,BLKSIZE=12901,RECFM=FBA)
//INFILE    DD DSN=<userid>.CRDEMO.DUMP.TRSBIN,DISP=SHR
//OUTFILE   DD DSN=<userid>.CRDEMO.DUMP,DISP=(NEW,CATLG),
//             STORCLAS=????????,
//*            UNIT=3390,VOL=SER=??????,
//             SPACE=(CYL,(500,50))
```


## 4.) Restore the Logical Dump
We will use the following JCL template to restore the logical dump:

* Sample JCL located in ```<InstallHLQ>.CRDEMO.JCL(RESTDEMO)```
* Change JOBCARD as appropriate for your system installation
* Change <userid> for your TSO userid on installation system
* Change <InstallHLQ> to 2-qual HLQ appropriate on your system
  * For SMS-managed target, use STORCLAS() keyword
  * For non SMS-managed target, use following ADRDSSU keywords
     - BYPASSACS(*)
     - NULLSTORCLAS
     - NULLMGMTCLAS
     - OUTDYNAM()

**Notes: The DSS logical dump was created from datasets under**
        
        
### WORKLOAD.ZSPARK (2 qualifiers) for the source libraries

You must do one of the following:

* RESTORE with RENAMEU(<InstallHLQ>)
                     -> Datasets will be under ```<InstallHLQ>.ZSPARK.CRDEMO.**```
* RESTORE with 2 HLQs on RENAMEU (as in example below)
                     -> Datasets will be under ```<InstallHLQ>.CRDEMO.**```
* Individually RENAME each library to add/remove
                     a qualifier specific to your installation
                     -> List of individual datasets contained within dump:
  * ```WORKLOAD.ZSPARK.CRDEMO.DB.CLIENTS.SORTED```
  * ```WORKLOAD.ZSPARK.CRDEMO.DB.SPPAYTS.SYSPUNCH```
  * ```WORKLOAD.ZSPARK.CRDEMO.DB.SPPAYTS.SYSREC```
  * ```WORKLOAD.ZSPARK.CRDEMO.DB2.DDL```
  * ```WORKLOAD.ZSPARK.CRDEMO.DB2.JCL```
  * ```WORKLOAD.ZSPARK.CRDEMO.DCLGEN```
  * ```WORKLOAD.ZSPARK.CRDEMO.JCL```
  * ```WORKLOAD.ZSPARK.CRDEMO.XLS.ZIPBIN```


**RESTDEMO JCL sample**

```
//RESTDEMO JOB ,,CLASS=A,MSGCLASS=H,NOTIFY=&SYSUID,REGION=0M
//*
//RESTORE  EXEC PGM=ADRDSSU
//SYSPRINT DD SYSOUT=*
//DDIN     DD DSN=<userid>.CRDEMO.DUMP,DISP=SHR
//SYSIN    DD *
  RESTORE DATASET(                            -
          INCLUDE(**))                        -
          RENAMEU(WORKLOAD.ZSPARK.**,         -
                  <InstallHLQ>.**)            -
    CATALOG                                   -
    NULLMGMTCLAS                              -
    STORCLAS(????????)                        -
    INDD(DDIN)
//
    BYPASSACS(*)                              -
    NULLSTORCLAS                              -
    NULLMGMTCLAS                              -
    OUTDYNAM(??????)                          -
```

## 5.) Ensure You Restored Successfully
After RESTDEMO Restore job completes, you should have the following:

* Install JCL:      ```<InstallHLQ>.CRDEMO.JCL```
* COPBOL COPYBOOK:  ```<InstallHLQ>.CRDEMO.DCLGEN```
* DB2 DDL/JCL:      ```<InstallHLQ>.CRDEMO.DB2.*```
* DB2 UNLOADs:      ```<InstallHLQ>.CRDEMO.DB.*```
* XLS WinZip:       ```<InstallHLQ>.CRDEMO.XLS.ZIPBIN```

**Continue w/Steps #6 thru #15 contained in the full version of the
README packaged within the dump in:** ```<InstallHLQ>.CRDEMO.JCL($$README)```

