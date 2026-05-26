NTDLL Undocumented Functions: Complete Reference
Table of Contents
Overview

Loader Functions (Ldr*)

Run-Time Library Functions (Rtl*)

System Service Dispatch Stubs (Nt*/Zw*)

Debugging Functions (Dbg/DbgUi)

Windows Subsystem Communication (Csr*)

Event Tracing Functions (Etw*)

Thread Pool Functions (Tp*)

C Runtime Functions

Data Exports

Discontinued Functions

References

Overview
The Windows NT Dynamic Link Library (ntdll.dll) is a core system library that serves as the user-mode interface to the Windows kernel. It contains:

System service dispatch stubs to Windows executive system services

Internal support functions used by subsystems and subsystem DLLs

The image loader (functions with the Ldr prefix)

The heap manager

Windows subsystem process communication functions (functions with the Csr prefix)

General run-time library routines (functions with the Rtl prefix)

User-mode debugging (functions with the DbgUi prefix)

Event Tracing for Windows (functions with the Etw prefix)

The user-mode APC dispatcher and exception dispatcher

A subset of the C Run-Time (CRT) routines

Note: Microsoft does not provide official documentation for most of these functions. They may change without notice between Windows versions. Use at your own risk in production code.

Loader Functions (Ldr*)
The loader functions handle dynamic linking, module loading, and symbol resolution. These are the low-level implementation of what kernel32.dll exposes as LoadLibrary, GetProcAddress, etc.

Core Loading Functions
Function	Description
LdrLoadDll	Loads a DLL into the process address space; called by LoadLibrary
LdrUnloadDll	Unloads a DLL; called by FreeLibrary
LdrGetDllHandle	Retrieves the base address of a loaded module by name
LdrGetDllHandleEx	Extended version with additional flags
LdrGetProcedureAddress	Resolves a function export by name or ordinal
LdrGetProcedureAddressEx	Extended version with lookup flags
Module Enumeration
Function	Description
LdrEnumerateLoadedModules	Iterates through all modules loaded in the process
LdrQueryProcessModuleInformation	Retrieves detailed module information for the process
LdrFindEntryForAddress	Finds the module entry (LDR_DATA_TABLE_ENTRY) containing an address
Resource Functions
Function	Description
LdrAccessResource	Accesses a resource embedded in a module
LdrFindResource_U	Locates a resource by type and name
LdrFindResourceDirectory_U	Finds a resource directory
LdrEnumResources	Enumerates all resources in a module
Reference Counting
Function	Description
LdrAddRefDll	Increments the reference count of a loaded DLL
LdrUnloadDll	Decrements reference count and unloads when zero
Loader Lock Management
Function	Description
LdrLockLoaderLock	Acquires the loader lock
LdrUnlockLoaderLock	Releases the loader lock
RtlIsThreadWithinLoaderCallout	Checks if the current thread is executing loader callbacks
Notifications and Callbacks
Function	Description
LdrRegisterDllNotification	Registers a callback for DLL load/unload events
LdrUnregisterDllNotification	Unregisters a DLL notification callback
LdrSetAppCompatDllRedirectionCallback	Sets DLL redirection for application compatibility
LdrSetDllManifestProber	Sets custom manifest probing function
Validation and Verification
Function	Description
LdrVerifyImageMatchesChecksum	Verifies that a module's checksum matches
LdrVerifyImageMatchesChecksumEx	Extended checksum verification
Utility Functions
Function	Description
LdrDisableThreadCalloutsForDll	Prevents thread attach/detach callbacks for a DLL
LdrQueryImageFileExecutionOptions	Queries image file execution options from registry
LdrGetFailureData	Retrieves data about a loader failure
LdrQueryModuleServiceTags	Queries service tags for a module
LdrProcessRelocationBlock	Processes relocation blocks
LdrInitializeThunk	Initializes thunk for module loading
LdrShutdownProcess	Performs loader shutdown during process termination
LdrShutdownThread	Performs loader cleanup during thread termination
Source: 

Run-Time Library Functions (Rtl*)
The Rtl prefix functions are general-purpose runtime utilities covering memory management, string handling, security, compression, and more.

Process and Thread Management
Function	Description
RtlSetProcessIsCritical	Marks current process as critical (termination causes BSOD)
RtlCreateProcessParameters	Creates RTL_USER_PROCESS_PARAMETERS structure
RtlDestroyProcessParameters	Frees process parameter structure
RtlNormalizeProcessParams	Normalizes process parameter structure
RtlDeNormalizeProcessParams	Reverses normalization
Memory Management
Function	Description
RtlAllocateHeap	Allocates memory from a heap
RtlFreeHeap	Frees memory allocated from a heap
RtlReAllocateHeap	Resizes a heap allocation
RtlSizeHeap	Returns the size of a heap allocation
RtlZeroHeap	Zeroes a heap (discontinued after 3.51)
RtlWalkHeap	Enumerates heap entries (discontinued after 3.51)
RtlGetUserFlagsHeap	Gets user flags for a heap allocation
RtlSetUserFlagsHeap	Sets user flags for a heap allocation
RtlGetUserValueHeap	Gets user value for a heap allocation
RtlSetUserValueHeap	Sets user value for a heap allocation
Compression and Decompression
Function	Description
RtlCompressBuffer	Compresses a buffer using specified algorithm
RtlDecompressBuffer	Decompresses a buffer
RtlDecompressFragment	Decompresses a fragment of compressed data
RtlGetCompressionWorkSpaceSize	Returns required workspace size for compression
String Manipulation
Function	Description
RtlAnsiStringToUnicodeString	Converts ANSI string to Unicode
RtlUnicodeStringToAnsiString	Converts Unicode string to ANSI
RtlOemStringToUnicodeString	Converts OEM string to Unicode
RtlUnicodeStringToOemString	Converts Unicode string to OEM
RtlxAnsiStringToUnicodeSize	Calculates required buffer size for ANSI→Unicode conversion
RtlxOemStringToUnicodeSize	Calculates required buffer size for OEM→Unicode conversion
RtlxUnicodeStringToAnsiSize	Calculates required buffer size for Unicode→ANSI conversion
RtlxUnicodeStringToOemSize	Calculates required buffer size for Unicode→OEM conversion
RtlIsTextUnicode	Determines if buffer contains Unicode text
Security and ACE Management
Function	Description
RtlAddAccessAllowedAce	Adds an ACCESS_ALLOWED_ACE to an ACL
RtlAddAccessDeniedAce	Adds an ACCESS_DENIED_ACE to an ACL
RtlAddAuditAccessAce	Adds an audit ACE to an ACL
RtlDeleteBoundaryDescriptor	Deletes a boundary descriptor
RtlCreateBoundaryDescriptor	Creates a boundary descriptor for namespace isolation
Time Conversion
The TIME_FIELDS structure is used by NTDLL time conversion routines:

c
typedef struct _TIME_FIELDS
{
    CSHORT Year;        // 1601...65535
    CSHORT Month;       // 1..12
    CSHORT Day;         // 1..31
    CSHORT Hour;        // 0..23
    CSHORT Minute;      // 0..59
    CSHORT Second;      // 0..59
    CSHORT Milliseconds;// 0..999
    CSHORT Weekday;     // 0..6 (Sunday..Saturday)
} TIME_FIELDS, *PTIME_FIELDS;
Function	Description
RtlTimeToTimeFields	Converts LARGE_INTEGER time to TIME_FIELDS
RtlTimeFieldsToTime	Converts TIME_FIELDS to LARGE_INTEGER time
RtlCutoverTimeToSystemTime	Special cutover time conversion
Source: 

Checksum and Hash Functions
Function	Description
RtlComputeCrc32	Computes CRC32 checksum
RtlComputeCrc32 variants	Additional CRC computation functions
Source: 

Callback and Synchronization
Function	Description
RtlAcquireSRWLockExclusive	Acquires SRW lock exclusively
RtlAcquireSRWLockShared	Acquires SRW lock shared
RtlRegisterSecureMemoryCacheCallback	Registers secure memory cache callback
RtlDeleteBoundaryDescriptor	Deletes boundary descriptor
Source: 

Unload Trace Functions
Function	Description
RtlGetUnloadEventTrace	Retrieves unload event trace
RtlGetUnloadEventTraceEx	Extended unload event trace
Source: 

System Service Dispatch Stubs (Nt*/Zw*)
These functions are the user-mode entry points to kernel system services. Each Nt* function has a corresponding Zw* function that behaves identically in user mode. They perform system calls via syscall (x64) or int 2e (x86) to transition to kernel mode.

Process Management
Function	Description	Documentation Status
NtCreateProcess	Creates a new process	Undocumented until 2007
NtCreateUserProcess	Creates a new user process (modern)	Not reliably exported
NtOpenProcess	Opens a handle to a process	Undocumented until 2007
NtTerminateProcess	Terminates a process	Variable
NtGetContextThread	Retrieves thread context	Variable
NtSetContextThread	Sets thread context	Variable
NtCreateThread	Creates a thread	Variable
NtOpenThread	Opens a thread handle	Undocumented 2004-2008
NtSuspendThread	Suspends a thread	Variable
NtResumeThread	Resumes a thread	Variable
NtAlertResumeThread	Resumes and alerts a thread	Variable
NtAlertThread	Alerts a thread	Variable
Memory Management
Function	Description	Documentation Status
NtAllocateVirtualMemory	Reserves or commits virtual memory	Undocumented until 2008-2009
NtFreeVirtualMemory	Releases or decommits virtual memory	Undocumented until 2008-2009
NtProtectVirtualMemory	Changes page protection	Variable
NtQueryVirtualMemory	Queries virtual memory information	Variable
NtMapViewOfSection	Maps a section view into process	Undocumented until 2007
NtUnmapViewOfSection	Unmaps a section view	Variable
NtCreateSection	Creates a memory section	Undocumented until 2007
NtOpenSection	Opens a memory section	Undocumented until 2007
NtExtendSection	Extends a section size	Variable
NtLockVirtualMemory	Locks pages into physical memory	Variable
NtUnlockVirtualMemory	Unlocks locked pages	Variable
NtFlushVirtualMemory	Flushes virtual memory range	Undocumented until 2008-2009
File and I/O Management
Function	Description	Documentation Status
NtCreateFile	Creates or opens a file	Settlement (2002)
NtOpenFile	Opens an existing file	Settlement (2002)
NtReadFile	Reads from a file	Documented
NtWriteFile	Writes to a file	Documented
NtDeviceIoControlFile	Sends control code to device	Undocumented until 2001-2003
NtFsControlFile	Sends FSCTL to filesystem	Undocumented until 2008-2009
NtFlushBuffersFile	Flushes file buffers	Undocumented until 2008-2009
NtLockFile	Locks a byte range in a file	Undocumented until 2008-2009
NtUnlockFile	Unlocks a byte range	Variable
NtCancelIoFile	Cancels pending I/O operations	Variable
NtDeleteFile	Deletes a file	Undocumented until 2008-2009
NtQueryInformationFile	Queries file information	Variable
NtSetInformationFile	Sets file information	Variable
NtQueryAttributesFile	Queries basic file attributes	Declared in Windows 10 WDK
NtCreateNamedPipeFile	Creates a named pipe	Variable
NtCreateMailslotFile	Creates a mailslot	Variable
NtNotifyChangeDirectoryFile	Monitors directory changes	Variable
Registry Management
Function	Description	Documentation Status
NtCreateKey	Creates a registry key	Undocumented until 2007
NtOpenKey	Opens a registry key	Undocumented until 2007
NtDeleteKey	Deletes a registry key	Undocumented until 2007
NtEnumerateKey	Enumerates subkeys	Undocumented until 2007
NtEnumerateValueKey	Enumerates key values	Undocumented until 2007
NtQueryValueKey	Queries a value	Variable
NtSetValueKey	Sets a value	Variable
NtDeleteValueKey	Deletes a value	Undocumented until 2007
NtFlushKey	Flushes key to disk	Undocumented until 2007
NtLoadKey	Loads a registry hive	Variable
NtUnloadKey	Unloads a registry hive	Variable
NtInitializeRegistry	Initializes registry (boot)	Variable
Object Management
Function	Description	Documentation Status
NtClose	Closes a kernel object handle	Undocumented until 2001-2002
NtDuplicateObject	Duplicates an object handle	Undocumented until 2008-2009
NtQueryObject	Queries object information	Variable
NtSetInformationObject	Sets object information	Declared in Windows 10 WDK
NtMakeTemporaryObject	Makes object temporary	Undocumented until 2007
Event and Synchronization
Function	Description	Documentation Status
NtCreateEvent	Creates an event object	Undocumented until 2008-2009
NtOpenEvent	Opens an event object	Undocumented until 2007
NtSetEvent	Signals an event	Variable
NtClearEvent	Resets an event	From NTDLL 3.50
NtResetEvent	Resets an event to non-signaled	Variable
NtWaitForSingleObject	Waits for an object	Variable
NtWaitForMultipleObjects	Waits for multiple objects	Variable
NtCreateMutant	Creates a mutant (mutex)	Variable
NtOpenMutant	Opens a mutant	Variable
NtCreateSemaphore	Creates a semaphore	Variable
NtOpenSemaphore	Opens a semaphore	Variable
NtCreateTimer	Creates a timer object	Variable
NtOpenTimer	Opens a timer	Variable
NtCancelTimer	Cancels a timer	Variable
NtCreateEventPair	Creates an event pair	Variable
NtOpenEventPair	Opens an event pair	Variable
I/O Completion
Function	Description	Documentation Status
NtCreateIoCompletion	Creates I/O completion port	From NTDLL 3.50
NtOpenIoCompletion	Opens I/O completion port	From NTDLL 3.50
NtRemoveIoCompletion	Removes completion entry	From NTDLL 3.50
NtSetIoCompletion	Sets I/O completion	Variable
Security and Token Management
Function	Description	Documentation Status
NtOpenProcessToken	Opens process token	Documented for Windows 2000+
NtOpenThreadToken	Opens thread token	Variable
NtDuplicateToken	Duplicates a token	Undocumented until 2008-2009
NtAdjustPrivilegesToken	Adjusts token privileges	Documented for Windows 2000+
NtAdjustGroupsToken	Adjusts token groups	Requires Windows XP+
NtPrivilegeCheck	Checks privilege	Requires Windows XP+
NtAccessCheck	Performs access validation	Variable
NtAccessCheckAndAuditAlarm	Access check with audit	Requires Windows XP+
LPC/ALPC (Local Procedure Call)
Function	Description
NtConnectPort	Connects to an LPC port
NtListenPort	Listens on an LPC port
NtAcceptConnectPort	Accepts an LPC connection
NtCompleteConnectPort	Completes an LPC connection
NtRequestPort	Sends a request to a port
NtRequestWaitReplyPort	Sends request and waits for reply
NtReplyPort	Sends a reply
NtReplyWaitReceivePort	Replies and waits for receive
NtImpersonateClientOfPort	Impersonates an LPC client
NtCreatePort	Creates an LPC port
Time and Timing
Function	Description	Documentation Status
NtQuerySystemTime	Gets system time	Variable
NtSetSystemTime	Sets system time	Variable
NtDelayExecution	Sleeps/delays execution	Variable
NtQueryTimerResolution	Gets timer resolution	Declared in Windows 10 WDK
NtSetTimerResolution	Sets timer resolution	Declared in Windows 10 WDK
NtGetTickCount	Gets tick count	Discontinued 5.1, restored 5.2
NtAllocateLocallyUniqueId	Allocates a LUID	Undocumented until 2007
System Information
Function	Description	Documentation Status
NtQuerySystemInformation	Queries system information	Variable
NtSetSystemInformation	Sets system information	Declared in Windows 10 WDK
NtQueryInformationProcess	Queries process information	Variable
NtSetInformationProcess	Sets process information	Variable
NtQueryInformationThread	Queries thread information	Variable
NtSetInformationThread	Sets thread information	Variable
Driver and Module Management
Function	Description	Documentation Status
NtLoadDriver	Loads a kernel driver	Undocumented until 2008-2009
NtUnloadDriver	Unloads a driver	Variable
NtQueryIntervalProfile	Queries profiling interval	Variable
NtSetIntervalProfile	Sets profiling interval	Variable
NtCreateProfile	Creates a profiling object	Variable
Directory/Object Directory Management
Function	Description	Documentation Status
NtCreateDirectoryObject	Creates a directory object	Undocumented until 2007
NtOpenDirectoryObject	Opens a directory object	Undocumented until 2008-2009
Plug and Play
Function	Description
NtPlugPlayControl	PnP control operations
NtGetPlugPlayEvent	Gets PnP events (discontinued in 6.2)
Display and Debug
Function	Description
NtDisplayString	Displays a string (blue-screen style)
NtCallbackReturn	Returns from a callback
NtContinue	Continues execution after exception
Sources: 

Debugging Functions (Dbg/DbgUi)
These functions provide user-mode debugging support.

Function	Description
DbgBreakPoint	Software breakpoint; whole implementation of DebugBreak (3.51-6.0); modern compilers use __debugbreak intrinsic
DbgPrint	Formatted string output to kernel debugger
DbgPrompt	Displays debug prompt (undocumented until 2005-2006)
DbgUserBreakPoint	User-mode breakpoint
DbgUiConnectToDbg	Connects to user-mode debugger
DbgUiContinue	Continues a debugged thread
DbgUiWaitStateChange	Waits for debug event state change
DbgSsHandleKmApiMsg	Handles kernel-mode API message (discontinued 5.1)
DbgSsInitialize	Initializes subsystem debugging (discontinued 5.1)
Source: 

Windows Subsystem Communication (Csr*)
The Csr prefix functions handle communication with the Client/Server Runtime Subsystem (CSRSS).

Function	Description	Status
CsrAllocateCaptureBuffer	Allocates a capture buffer for CSRSS	
CsrAllocateCapturePointer	Allocates capture pointer (discontinued 5.0)	
CsrAllocateMessagePointer	Allocates message pointer	
CsrCaptureMessageBuffer	Captures message buffer	
CsrCaptureMessageString	Captures message string	
CsrCaptureTimeout	Captures timeout value	
CsrClientCallServer	Calls a CSRSS server routine	
CsrClientConnectToServer	Connects to CSRSS server	
CsrClientMaxMessage	Gets max message size (discontinued 4.0)	
CsrClientSendMessage	Sends message to CSRSS (discontinued 4.0)	
CsrClientThreadConnect	Connects thread to CSRSS (discontinued 4.0)	
CsrFreeCaptureBuffer	Frees capture buffer	
CsrIdentifyAlertableThread	Identifies alertable thread	
CsrNewThread	Notifies CSRSS of new thread (discontinued 6.0 SP1)	
CsrProbeForRead	Probes buffer for read access (discontinued 6.0)	
CsrProbeForWrite	Probes buffer for write access (discontinued 6.0)	
CsrSetPriorityClass	Sets CSRSS thread priority class	
CsrpProcessCallbackRequest	Processes callback request (discontinued 4.0)	
Source: 

Event Tracing Functions (Etw*)
These functions provide Event Tracing for Windows (ETW) support.

Function	Description
EtwEventWriteEx	Extended event writing (exported from NTDLL, forwarded in ADVAPI32 6.2+)
EtwEventRegister	Registers an ETW provider
EtwEventUnregister	Unregisters an ETW provider
EtwEventWrite	Writes an ETW event
Source: 

Thread Pool Functions (Tp*)
These functions manage Windows thread pools.

Function	Description
TpAllocPool	Allocates a thread pool
TpReleasePool	Releases a thread pool
TpAllocCleanupGroup	Allocates cleanup group
TpReleaseCleanupGroup	Releases cleanup group
TpReleaseCleanupGroupMembers	Releases cleanup group members
TpAllocTimer	Allocates a timer
TpReleaseTimer	Releases a timer
TpAllocWait	Allocates a wait object
TpReleaseWait	Releases a wait object
TpAllocWork	Allocates a work item
TpReleaseWork	Releases a work item
TpAllocIoCompletion	Allocates I/O completion
TpReleaseIoCompletion	Releases I/O completion
TpCallbackMayRunLong	Indicates callback may run long
TpCallbackIndependent	Sets callback independent flag
TpDisassociateCallback	Disassociates callback from group
TpCancelAsyncIoOperation	Cancels async I/O operation
TpSetPoolMaxThreads	Sets max threads for pool
TpSetPoolMinThreads	Sets min threads for pool
TpWaitForWork	Waits for work completion
Source: 

C Runtime Functions
NTDLL exports a subset of C Runtime Library functions. These are the actual implementations that higher-level CRTs (like MSVCRT) may call into.

String Functions
Function	Description
strcat	Concatenates strings
strchr	Finds character in string
strcmp	Compares strings
strcpy	Copies string
strcspn	Gets span of characters not in set
strlen	Gets string length
strncat	Concatenates with length limit
strncmp	Compares with length limit
strncpy	Copies with length limit
strpbrk	Finds first character from set
strrchr	Finds last character
strspn	Gets span of characters in set
strstr	Finds substring
wcscat	Wide-character concatenation
wcschr	Wide-character find char
wcscmp	Wide-character compare
wcscpy	Wide-character copy
wcscspn	Wide-character span complement
wcslen	Wide-character length
wcsncat	Wide-character limited concatenation
wcsncmp	Wide-character limited compare
wcsncpy	Wide-character limited copy
wcspbrk	Wide-character find from set
wcsrchr	Wide-character find last
wcsspn	Wide-character span
wcsstr	Wide-character find substring
wcstok	Wide-character tokenization (discontinued 4.0)
wcstol	Wide-character to long
wcstoul	Wide-character to unsigned long
wcstombs	Wide-character to multibyte
mbstowcs	Multibyte to wide-character
Character Classification
Function	Description
isalpha	Alphabetic character test
isdigit	Digit test
islower	Lowercase test
isprint	Printable test
isspace	Whitespace test
isupper	Uppercase test
isxdigit	Hexadecimal digit test
iswalpha	Wide-character alphabetic
iswctype	Wide-character classification
tolower	Convert to lowercase
toupper	Convert to uppercase
towlower	Convert wide to lowercase
towupper	Convert wide to uppercase
Case-Insensitive Functions
Function	Description
_strcmpi	Case-insensitive string compare
_stricmp	Case-insensitive compare
_strlwr	Convert string to lowercase
_strnicmp	Limited case-insensitive compare
_strupr	Convert string to uppercase
_wcsicmp	Wide-case-insensitive compare
_wcslwr	Wide to lowercase
_wcsnicmp	Limited wide case-insensitive
_wcsupr	Wide to uppercase
Conversion Functions
Function	Description
_itoa	Integer to string
_ltoa	Long to string
_ultoa	Unsigned long to string
atoi	ASCII to integer
atol	ASCII to long
abs	Absolute value
labs	Long absolute value
Memory Functions
Function	Description
memchr	Find byte in memory
memcmp	Compare memory blocks
memcpy	Copy memory block
memmove	Move memory block (handles overlap)
memset	Set memory block
_memccpy	Copy until character found
_memicmp	Case-insensitive memory compare
Math Functions
Function	Description
atan	Arctangent
ceil	Ceiling
cos	Cosine
fabs	Absolute float value
floor	Floor
log	Natural logarithm
pow	Power
sin	Sine
sqrt	Square root
tan	Tangent
Formatted Output
Function	Description
sprintf	Formatted output to string
vsprintf	va_list version of sprintf
_snprintf	Safe limited sprintf
_snwprintf	Safe limited wide sprintf
swprintf	Wide formatted output
sscanf	Formatted input from string
Path/String Utilities
Function	Description
_splitpath	Splits path into components
qsort	Quick sort
Floating-Point/Stack Support (x86)
Function	Description
_fltused	Floating-point usage flag (data)
_ftol	Float to long conversion
_chkstk	Stack checking/probing
_CIpow	Complex integer power
Exception Handling (discontinued)
Function	Description
_except_handler2	Exception handler (discontinued 3.51)
_global_unwind2	Global unwind (discontinued 3.51)
_local_unwind2	Local unwind (discontinued 3.51)
_abnormal_termination	Abnormal termination (discontinued 3.51)
Source: 

Data Exports
NTDLL exports several data objects.

Data Export	Description
NlsMbCodePageTag	Multi-byte code page tag (documented 2008-2009 as NLS_MB_CODE_PAGE_TAG macro)
NlsMbOemCodePageTag	Multi-byte OEM code page tag
Source: 

Discontinued Functions
These functions existed in earlier versions but have been removed.

Function	Discontinued In	Notes
CsrAllocateCapturePointer	Version 5.0	
CsrClientMaxMessage	Version 4.0	
CsrClientSendMessage	Version 4.0	
CsrClientThreadConnect	Version 4.0	
CsrNewThread	Windows Vista SP1 (6.0)	
CsrProbeForRead	Version 6.0	
CsrProbeForWrite	Version 6.0	
CsrpProcessCallbackRequest	Version 4.0	
DbgSsHandleKmApiMsg	Version 5.1	
DbgSsInitialize	Version 5.1	
NtEnumerateBus	Version 4.0	
NtGetPlugPlayEvent	Version 6.2	
NtGetTickCount	Version 5.1 (restored 5.2)	
RtlGetUserFlagsHeap	Version 3.51	
RtlGetUserValueHeap	Version 3.51	
RtlSetUserFlagsHeap	Version 3.51+	Deprecated
RtlSetUserValueHeap	Version 3.51+	Deprecated
RtlWalkHeap	After 3.51	
RtlZeroHeap	After 3.51	
wcstok	Version 4.0	
_except_handler2	Version 3.51	
_global_unwind2	Version 3.51	
_local_unwind2	Version 3.51	
_abnormal_termination	Version 3.51	
Source: 

Detailed Documentation: RtlSetProcessIsCritical
The function originally requested is documented in detail below.

Function Signature
c
NTSYSAPI NTSTATUS NTAPI RtlSetProcessIsCritical(
    _In_     BOOLEAN NewValue,
    _Out_opt_ PBOOLEAN OldValue,
    _In_     BOOLEAN CheckFlag
);
Description
Sets or clears the critical status of the current process. A critical process, if terminated, causes the system to crash with a bugcheck (BSOD). This function is used internally by essential system processes such as csrss.exe, lsass.exe, services.exe, smss.exe, svchost.exe, and wininit.exe.

Parameters
Parameter	Type	Description
NewValue	BOOLEAN	TRUE to mark process as critical, FALSE to remove critical status
OldValue	PBOOLEAN (optional)	Receives previous critical status; can be NULL
CheckFlag	BOOLEAN	When TRUE, requires FLG_ENABLE_SYSTEM_CRIT_BREAKS (0x00100000) in NtGlobalFlag
Return Value
Return Value	Description
STATUS_SUCCESS (0x00000000)	Operation successful
STATUS_UNSUCCESSFUL	CheckFlag TRUE but system critical breaks disabled
STATUS_PRIVILEGE_NOT_HELD	Caller lacks SeDebugPrivilege
Behavior
If CheckFlag is non-zero and NtGlobalFlag lacks 0x00100000, returns STATUS_UNSUCCESSFUL

If OldValue non-NULL, stores current critical status (default FALSE on query failure)

Calls NtSetInformationProcess with ProcessBreakOnTermination (0x1D) to set BreakOnTermination flag in EPROCESS

Effects on System
When a critical process terminates:

Without kernel debugger: Bugcheck CRITICAL_PROCESS_DIED (0xEF) or CRITICAL_OBJECT_TERMINATION (0xF4)

With kernel debugger: Debugger prompt allowing break or ignore

Privilege Requirements
SeDebugPrivilege required (typically needs administrative privileges).

Example enablement code:

c
BOOL EnableDebugPrivilege() {
    HANDLE hToken;
    TOKEN_PRIVILEGES tp;
    LUID luid;

    if (!OpenProcessToken(GetCurrentProcess(), TOKEN_ADJUST_PRIVILEGES | TOKEN_QUERY, &hToken))
        return FALSE;

    if (!LookupPrivilegeValue(NULL, SE_DEBUG_NAME, &luid)) {
        CloseHandle(hToken);
        return FALSE;
    }

    tp.PrivilegeCount = 1;
    tp.Privileges[0].Luid = luid;
    tp.Privileges[0].Attributes = SE_PRIVILEGE_ENABLED;

    BOOL result = AdjustTokenPrivileges(hToken, FALSE, &tp, sizeof(tp), NULL, NULL);
    CloseHandle(hToken);
    return result;
}
Usage Example
c
// Make current process critical
NTSTATUS status = RtlSetProcessIsCritical(TRUE, NULL, FALSE);
if (status == STATUS_SUCCESS) {
    // Process is now critical - termination will cause BSOD
}

// Remove critical status
status = RtlSetProcessIsCritical(FALSE, NULL, FALSE);
Using Undocumented NTDLL Functions
Dynamic Loading (Recommended)
c
typedef NTSTATUS (NTAPI* pRtlSetProcessIsCritical)(BOOLEAN, PBOOLEAN, BOOLEAN);

HMODULE hNtdll = GetModuleHandleW(L"ntdll.dll");
pRtlSetProcessIsCritical RtlSetProcessIsCritical = 
    (pRtlSetProcessIsCritical)GetProcAddress(hNtdll, "RtlSetProcessIsCritical");

if (RtlSetProcessIsCritical) {
    RtlSetProcessIsCritical(TRUE, NULL, FALSE);
}
Header-only Library Approach
Some developers use header-only libraries that provide type-safe access to NTDLL functions:

cpp
#include "NtDll.h"

// Instead of manual GetProcAddress
DWORD crc = NtDll::RtlComputeCrc32(0, data, sizeof(data));
Direct System Calls
Nt* functions in ntdll.dll are system call stubs. Some security tools bypass user-mode hooks by directly invoking these system calls rather than calling the ntdll stub:

asm
; Example NtReadVirtualMemory syscall pattern (x64)
mov r10, rcx
mov eax, SYSTEM_CALL_NUMBER
syscall
ret
The system call number changes between Windows versions, requiring dynamic extraction.

Windows Version Notes
Windows 3.51: Earliest version analyzed; 136 exports newly added

Windows XP (5.1): RtlSetProcessIsCritical first appears

Windows Vista (6.0): Major changes; many Csr* functions discontinued

Windows 7 (6.1): Thread pool functions (Tp*) prominent in kernel32 forwarding

Windows 10 WDK: Some functions like ZwQueryTimerResolution declared but still undocumented

References
Geoff Chappell - Loader Functions

Geoff Chappell - NTDLL 3.51 Exports

Geoff Chappell - NTDLL 3.50 Exports

Geoff Chappell - KERNEL32 6.0 Exports

NtDoc - TIME_FIELDS documentation

GitHub - ntdll-usermode-ram-read-write

GitHub - NtDll header-only library

GitHub - Windows Subsystems Documentation

Disclaimer
This documentation is based on reverse engineering and community research. Function signatures, behaviors, and availability may vary between Windows versions and updates. Microsoft does not provide support for undocumented functions, and they may change or be removed without notice. Use these functions only after thorough testing in your target environment.
