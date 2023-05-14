# CVE-2022-24481-POC

## TL;DR
This poc is reversed from exp(SHA1:fec933b0c46366a1fb272bfaf82a8262597a8d75) and many AV engine detected it as CVE-2022-24521. After analyze it, I find that it maybe the exp for CVE-2022-24481, bacause CVE-2022-24521 is not a type confusion vulnerability.
The bug cause by incorrect of point CLFS_CONTAINER_CONTEXT.pContainer, it will be called in function CClfsContainer::Close, so if it was modified before, and it will execute any code. but how to modify this point?
the RCA of exp(fec933b0c46366a1fb272bfaf82a8262597a8d75) is as follow:
1. exp modify CLFS_BASE_RECORD_HEADER.rgClients to the offset of CLFS_CONTAINER_CONTEXT.
2. in function CClfsLogFcbPhysical::Initialize, read some value into memory from CLFS_BASE_RECORD_HEADER.rgClients(the offset of CLFS_CONTAINER_CONTEXT).
3. CClfsBaseFilePersisted::LoadContainerQ, it will generate the class Container point and fill into CLFS_CONTAINER_CONTEXT.pContainer.
4. in function CClfsRequest::Cleanup, write the value from step 2 into client context which is the same as container context in the memory.
5. in function CClfsRequest::Close, parse CLFS_CONTAINER_CONTEXT and call pContainer(trigger).
