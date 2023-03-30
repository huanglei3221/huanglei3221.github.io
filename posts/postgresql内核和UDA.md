UDA和



内存管理，参见： 

![image-20230328153926249](assets/images/postgresql内核和UDA/image-20230328153926249.png)

![image-20230328154001171](assets/images/postgresql内核和UDA/image-20230328154001171.png)



Context本身是一个trunk， trunk里面又分了block。有freelist来管理当前空闲的block

![image-20230328154108439](assets/images/postgresql内核和UDA/image-20230328154108439.png)



freelist里面又会记录每种大小的block分别分配了多少

![image-20230328154511194](assets/images/postgresql内核和UDA/image-20230328154511194.png)