# Changelog

## Version 2.0.0

#### system
* **Introduced multi-wait infrastructure, allowing for user mode synchronization primitives. Users are advised to stop using svcWaitSynchronization(Single) manually and move to the new wait API.**
* **Added UEvent and UTimer user mode synchronization primitives.**
* **Added full list of KernelError result codes, as well as a KERNELRESULT macro.**
* Added eventActive.
* Added tmemCreateFromMemory.
* Added syscall: svcTerminateDebugProcess.
* Corrected syscalls: svcMapPhysicalMemory, svcUnmapPhysicalMemory.
* Fixed bug in ipcParseDomainResponse.
* Fixed bug in ipcPrepareHeaderForDomain.
* Added serviceSendObject.

#### graphics
* **Major refactor and redesign of the entire libnx graphics stack.**
  * **Introduced NWindow (Native Window) API**, allowing for direct management of a renderable surface. It is possible to create a NWindow out of a ViLayer (or other sources of an IGBP binder object); or use nwindowGetDefault to retrieve the default native window.
  * **Introduced Framebuffer API** (used for creating and managing a software rendered framebuffer on a NWindow).
  * **Deprecated the old gfx API, scheduled for removal in the next libnx release**. Users are advised to move to the new NWindow API (and Framebuffer if applicable). Please see switch-examples for more information on how to use this new API.
  * **The default software rendered console backend now uses NWindow and Framebuffer** instead of the old gfx API. Therefore, it is now **mandatory** to use consoleUpdate/consoleExit.
  * Optimized software console scrolling; now using 128-bit copies and RGB565 framebuffer format (which requires 50% less memory bandwidth).
  * Completely redesigned Nvidia ioctl wrapper objects, now more closely matching official logic.
  * Miscellaneous fixes in vi, parcel, IGBP, etc code.
* Added vi commands: viSetContentVisibility, viGetDisplayLogicalResolution, viSetDisplayMagnification, viSetDisplayPowerState, viSetDisplayAlpha, viGetDisplayMinimumZ, viGetDisplayMaximumZ, viSetLayerSize, viSetLayerZ, viSetLayerPosition.
* Improved ViScalingMode enum.

#### applet
* **Introduced libapplet (library applet) launching support**.
* **Added swkbd (software keyboard) libapplet wrapper**.
* Added applet commands: appletPopLaunchParameter, appletPushToGeneralChannel, appletSetTerminateResult, appletSetMediaPlaybackState.
* Added account applet wrapper: accountGetPreselectedUser.
* Added libappletRequestHomeMenu, libappletRequestJumpToSystemUpdate.
* Added AppletStorage object.
* Added AppletHolder object.
* Added LibAppletArgs object.
* Corrected and added missing AppletFocusState and AppletFocusHandlingMode enum values.
* Now using domains for am services.

#### usb
* Added usb:hs service wrapper.
* Now using domains for usb:ds.
* Miscellaneous fixes and refactoring.

#### other services
* Added fs command: fsIsExFatSupported.
* Added hid command: hidAcquireNpadStyleSetUpdateEventHandle.
* Added ldr:ro and ro:dmnt service wrappers.
* Added ns:dev commands: nsdevLaunchProgram, nsdevGetShellEvent, nsdevGetShellEventInfo, nsdevTerminateApplication, nsdevPrepareLaunchProgramFromHost, nsdevLaunchApplication, nsdevLaunchApplicationWithStorageId, nsdevIsSystemMemoryResourceLimitBoosted, nsdevGetRunningApplicationProcessId, nsdevSetCurrentApplicationRightsEnvironmentCanBeActive.
* Added pm:dmnt commands: pmdmntGetDebugProcesses, pmdmntDisableDebug.
* Added pm:shell commands: pmshellTerminateProcessByProcessId, pmshellGetProcessEvent, pmshellGetProcessEventInfo, pmshellFinalizeDeadProcess, pmshellClearProcessExceptionOccurred, pmshellNotifyBootFinished, pmshellBoostSystemMemoryResourceLimit.
* Renamed ldrDmntGetNsoInfos to ldrDmntGetModuleInfos.
* Changed psm wrapper to dynamically open and close IPC sessions instead of leaving one open at all times. Introduced PsmSession object, used to manage this session.
* Fixed IPC bug in splSetConfig.

#### miscellaneous
* Added sys/poll.h as an alias for poll.h.
* Fixed compatibility with C99.
* Further improvements to overall system stability and other minor adjustments to enhance the user experience.

## Version 1.6.0

#### system
* **Added support for userland exception handling**.

#### filesystem
* **Added support for retrieving file timestamps on 3.0.0+**.
* Added fsdevSetArchiveBit, for setting the archive bit on a directory.
* Added fs commands: fsFsQueryEntry, fsFsSetArchiveBit, fsDeviceOperatorIsGameCardInserted, fsDeviceOperatorGetGameCardHandle, fsDeviceOperatorGetGameCardAttribute.

#### audio
* **Added hwopus service wrapper**.
* **Added audren support for system versions prior to 3.0** by automatically using the latest available revision number for a given version (1.0.0-4.0.0+).
* Added auddev (IAudioDevice) service wrapper.

#### hid
* **Added support for SL/SR buttons on right JoyCons**.
* Added HidJoyHoldType enum.
* Changed hid to use hidSetNpadJoyHoldType during init/exit.

#### other services
* Added psc (psc:m) service wrapper.
* Added spsm service wrapper.
* Added pcv service wrapper.
* Added lbl service wrapper.
* Added i2c service wrapper.
* Added gpio service wrapper.
* Added bpc commands: bpcGetSleepButtonState.
* Added setsys commands: setsysBindFatalDirtyFlagEvent, setsysGetFatalDirtyFlags.
* Added fatal commands: fatalWithContext.
* Added PsmBatteryVoltageState enum.
* Added SetSysFlag_RequiresRunRepairTimeReviser.
* Added appletSetFocusHandlingMode (previously it existed but it was not exposed).

#### miscellaneous
* Further improvements to overall system stability and other minor adjustments to enhance the user experience.

## Version 1.5.0

#### system
* **Improved service IPC support for domains**.
* Added RandomSeed HBABI key handling, which fixes broken/identical random number generation output when launching homebrew NROs multiple times with nx-hbloader.
* Added Barrier synchronization primitive.
* Added threadGetCurHandle.
* Fixed several C11 thread support standard compliance issues.
* Fixed virtmem handling on 1.x.
* Fixed ProcessState enumeration names and descriptions.
* Fixed bug in eventCreate.
* Fixed bug in jitCreate.

#### services
* fsp-srv and fsp-ldr services:
  * **Now using domains for IPC**, which solves problems related to having a large number of filesystem resources open.
  * Added fsdevGetDeviceFileSystem.
* time services:
  * Added timezone support.
  * The user system clock is now used by default instead of the network clock.
  * The user system clock is now also used as fallback if the specified system clock override (__nx_time_type) is not available.
* applet services:
  * **Added support for running with nx-hbloader as an Application**.
  * **Added support for enabling video recording** when running as an Application, although it needs a specific flag set in the host title's nacp to be accessible.
  * Added appletLockExit/appletUnlockExit, which can be used to ensure that user processes get a chance to finish before the application is closed by the system.
  * Added __nx_applet_exit_mode, used to control the application's behavior on close (including self-exit support).
  * Added AppletHookType_OnExitRequest.
  * Fixed issues related to running as an Application.
  * Minor internal refactor to use Event objects instead of raw handles.
* hid (input) services:
  * **Added SixAxisSensor support**.
  * Vibration partially fixed, although it needs disable/enable in system settings for it to be effective.
  * Added hidSetSupportedNpadStyleSet, hidSetSupportedNpadIdType, hidSetNpadJoyHoldType, hidGetControllerType, hidGetControllerColors, hidIsControllerConnected.
* USB services:
  * **Major refactor which adds support for 5.x+ systems**.
  * Added usbCommsSetErrorHandling, which now disables USB fatal errors by default.
* audin/audout services: minor internal refactor to use Event objects instead of raw handles.
* Added pm:shell command: pmshellGetApplicationPid.
* Added set:sys command: setsysGetFirmwareVersion.
* Added psm commands: psmGetBatteryVoltageState, psmBindStateChangeEvent, psmWaitStateChangeEvent, psmUnbindStateChangeEvent.
* fatal services: on systems prior to 3.x, FatalType_ErrorReportAndErrorScreen is used again due to FatalType_ErrorScreen not existing. **Beware of fatal errors on 1.x and 2.x** since they now create reports again (instead of silently hanging the system).

#### graphics
* **Refactored console device**:
  * The console now has an interface that can be used to override the default software rendering backend with a custom implementation (including GPU rendering).
  * The software renderer now takes care of calling gfxInitDefault, gfxSwapBuffers, gfxExit. gfx functions should not be called explicitly by console users.
  * Added consoleUpdate and consoleExit.
  * Old code is still compatible, although it is **strongly advised** to update it to use the new functions instead of calling gfx functions directly.
* Fixed NvFence regression on 1.x.
* Increased default nvservices transfermem heap size to 8 MB, which allows for more complex GPU homebrew to run.
* Changed nvBufferCreate to support different settings for cpu/gpu cacheability.

#### network
* Added nifm handling to socketInitialize/Exit, which makes gethostid/gethostname work by default.
* Added missing declarations to netinet/in.h.
* Changed nifm to use IPC domains.
* Optimized select/poll to avoid using malloc when possible.
* Fixed poll to accept -1 fds.

#### miscellaneous
* Further improvements to overall system stability and other minor adjustments to enhance the user experience.

## Version 1.4.1

* Restored compatibility with C++11.
* Added arm/thread_context.h, containing definitions for the ThreadContext structure.
* Added new syscalls: svcGetDebugThreadContext, svcSetDebugThreadContext, svcGetThreadContext3.
* Fixed signature of svcContinueDebugEvent, which changed in 3.0. The old signature is available as svcLegacyContinueDebugEvent.
* Added threadDumpContext.
* Added ipcCloneSession.
* Added gfxAppendFence.
* psm services: added psmGetChargerType.
* pm:dmnt services: fixed for 5.0+.
* nv services: added nvIoctl2 & handling for a separate cloned session, matching official software.
* Nvidia ioctl wrappers: added nvioctlNvhostAsGpu_UnmapBuffer, nvioctlNvmap_Free, nvioctlChannel_KickoffPb
* Further changes, fixes and improvements to the experimental Nvidia wrapper objects, which are used by the ported mesa/nouveau-based OpenGL stack.
* Further improvements to overall system stability and other minor adjustments to enhance the user experience.

## Version 1.4.0

#### system
* **Added support for C11 threads**, which are preemptively multitasked and load balanced across cores.
* **Added Event object**, which wraps kernel revent/wevent handles with optional autoclear.
* **Changed CondVar interface** to have the mutex be passed to condvarWait* instead of condvarInit, which is consistent with both the concept of a condition variable and with other common threading APIs.
* Added armGetSystemTick (which supersedes svcGetSystemTick), and armGetSystemTickFreq.
* Added rwlockInit.
* Added kernelAbove600.
* Added system calls: svcGetThreadCoreMask, svcSetThreadCoreMask.
* Added MOD0 header to binaries compiled with libnx.
* Fixed semaphoreTryWait.
* Fixed a memory leak in tmemCreate.

#### services
* Added bpc service (used for rebooting and shutting down the console).
* Added psm service (needed to get the battery status).
* Added ns commands: nsListApplicationRecord, nsListApplicationContentMetaStatus.
* Minor enhancements to applet service:
  * Added missing AppletType_SystemApplet initialization in applet code.
  * Added appletBeginBlockingHomeButton and appletEndBlockingHomeButton.
* The Event object is now used to return system events from service wrappers when possible, also providing the correct autoclear mode.
* Corrected fsOpenFileSystem and fsOpenFileSystemWithId.
* Corrected a bug in hidInitializeVibrationDevices.
* Corrected a bug in socket error conversion.
* Fixed nifm not initializing properly for < 3.0.0.
* Service manager (sm) session now closes properly.
* hid, irs, vi and nv services now acquire a reference to applet services.

#### audio
* **Added audren:u** service wrapper (presently requiring 3.0.0+, will be addressed in a future update).
* **Added AudioDriver** wrapper around audren, providing a higher level interface that can be used to mix and play sounds.

#### graphics
* **Major rewrite and refactoring work in the gfx wrapper** which brings reliability and usability improvements:
  * **Removed GfxMode_TiledSingle** mode due to it causing problems and potential (temporary) hardware damage.
  * **The default transform behavior no longer vertically flips the framebuffer**.
  * **Removed gfxSetDrawFlip** since it's no longer needed thanks to the change in the default transform behavior.
  * **It is not necessary to call gfxWaitForVsync in most situations** because gfxSwapBuffers already implicitly synchronizes with the display (this is mandated by the Android surface compositor and buffer producer system).
  * **Dequeue fatal errors should be solved**.
  * Simplified and streamlined logic.
  * nvgfx stripped down to the minimum that is actually necessary to allocate framebuffers.
  * Binder logic now more closely matches both Android code and official software.
  * Proper fence and event wait code is now used.
* **Console code no longer performs a forced flush/swap/vblank wait when printing a newline** due to performance reasons. Users of the console device must make sure that gfxFlushBuffers and gfxSwapBuffers are periodically called, preferably in the main loop of the application.
* **Added experimental wrapper objects for the Nvidia driver**, needed in order to use the GPU. These wrappers are still in RE phase and will be subject to change in a future release.
* **Major redesign of the VI service wrapper** that allows future users to use VI directly to create a display layer.
* Binder services & buffer producer wrappers were enhanced and redesigned.
  * Binder now holds less state and always uses the VI binder relay service session.
  * Added Module_LibnxBinder error codes.
  * IGraphicBufferProducer binder service wrappers now have the `bq` prefix and explicitly accept a Binder object.
  * Added bqCancelBuffer.
  * bqGraphicBufferInit was renamed to bqSetPreallocatedBuffer.
* Enhancements and additions to nvidia ioctl wrappers.
* Added definitions for some more Android enumerations.

#### miscellaneous
* The `ALIGN` macro was removed in favor of the C11/C++11 alignas attribute.
* Further improvements to overall system stability and other minor adjustments to enhance the user experience.

## Version 1.3.2

* **Fixed critical IPC bug in fatalWithType that rendered the type parameter ignored (necessary for error report creation prevention)**.

## Version 1.3.1

* **Fixed regression in cwd handling**.
* Added fs commands: fsOpenBisStorage, fsOpenBisFilesystem, fsOpenFileSystem(WithId), fsStorageWrite/Flush/GetSize/SetSize, fsFsCleanDirectoryRecursively.
* Added ncm commands: ncmContentMetaDatabaseList/DisableForcibly, ncmContentStorageXYZ family of functions.
* HID service now works in sysmodules.
* Fixed errors in ncmContentMetaDatabaseListApplication and ncmContentMetaDatabaseSet/Get.
* Fixed data races in kernel version detection functions.
* Fixed return-from-main to honor atexit handlers.
* Further improvements to overall system stability and other minor adjustments to enhance the user experience.

## Version 1.3.0

#### system
* **Fixed critical bug in mutexUnlock**.
* **Expanded support for standard time functions**, including gettimeofday, clock functions and nanosleep.
* Added Semaphore synchronization primitive.
* Added system calls: svcGetDebugThreadParam, svcGetThreadList.
* Improved IpcParsedCommand support for domain message information.

#### services
* **fatalSimple no longer creates an error report that gets uploaded to Nintendo's servers** - also added fatalWithType.
* Added ncm service.
* Added nifm service.
* Added ns:dev service.
* Added ns:vm service.
* Added account commands: accountGetUserCount, accountListAllUsers.
* Added ns commands: nsGetTotalSpaceSize, nsGetFreeSpaceSize.
* Added pm:shell commands: pmshellTerminateProcessByTitleId.
* Added set:sys commands: setsysGetFlag, setsysSetFlag, setsysGetSettingsItemValueSize, setsysGetSettingsItemValue, setsysGetColorSetId, setsysSetColorSetId.

#### sockets
* **Fixed errno** - now properly translating Horizon BSD errno to newlib errno values.
* Fixed bugs in inet_pton4, ioctl, socketDeserializeAddrInfo.
* Fixed _IOC macro.
* Fixed netdb.h by readding socklen_t definition.
* Implemented gethostid and gethostname.

#### miscellaneous
* Added overridable userAppInit and userAppExit functions that add to instead of replacing __appInit and __appExit.
* Now embedding GNU build id in compiled binaries, used by creport crash dumps (such as those generated by Atmosphère's creport implementation).
* Further improvements to overall system stability and other minor adjustments to enhance the user experience.

## Version 1.2.1

* Added hidMergeSingleJoyAsDualJoy.
* Added setGetSerialNumber.
* Renamed the JIT syscalls to match their official names.
* Fixed bug in nvgfxEventWait that prevented the graphics subsystem from working on certain conditions.
* Fixed JIT support on 5.0.0+ by falling back on JitType_CodeMemory.
* Further improvements to overall system stability and other minor adjustments to enhance the user experience.

## Version 1.2.0

#### filesystem
* Revise fsdev initialization
  * Removed fsdevInit/Exit
  * Now automatically initializing fsdev state on first mount
  * Added fsdevMountSdmc (replaces fsdevInit)
  * Added fsdevUnmountAll (replaces fsdevExit)
* Add FS commands for SD card state change detection.
* Added mounting for SystemSaveData.
* Use Service for all fs sessions. This fixes an issue with savedata commit.
* Implemented FsSaveDataIterator (aka ISaveDataInfoReader).
* Changed ContentStorageId in FsSave to SaveDataType.
* Added FsStorageId.
* Added enums FsSaveDataSpaceId and FsSaveDataType.
* Removed FS_MOUNTSAVEDATA_INVAL_DEFAULT/FS_MOUNTSYSTEMSAVEDATA_INVAL_DEFAULT.

####  hid updates
* Added hidGetHandheldMode().
* Added hidSetNpadJoyAssignmentModeSingleByDefault()
* hidSetNpadJoyAssignmentModeDual().
* Check serviceIsActive() in hidExit().
* Use hidSetNpadJoyAssignmentModeDual() for all controllers during hidInitialize()/hidExit().
* Full hid vibration support for pre-4.0.0 hid commands.

#### network
* add support for finding nxlink host
* improve bsdSockets support
* added nxlink stdio redirection
* Fix IN6_IS_ADDR macros in netinet/in.h (#68)

#### system
* Added support for Services: fsp-ldr, fsp-pr, lr, csrng, spl, pm:info, sm:m, pl, ns, all Loader services.
* IPC: Improve information available in IpcParsedCommand
* Added system calls: svcCreatePort, svcConnectToPort, svcUnmapProcessMemory, svcGetSystemInfo, svcSetThreadPriority, svcGetCurrentProcessorNumber, svcSignalEvent, svcSendSyncRequestWithUserBuffer, svcSendAsyncRequestWithUserBuffer, svcGetThreadId, svcReplyAndReceiveWithUserBuffer, svcCreateEvent, svcReadWriteRegister, svcCreateInterruptEvent, svcMapDeviceAddressSpaceByForce, svcTerminateProcess, svcMap/UnmapPhysicalMemory(Unsafe), svcSetUnsafeLimit, svcGetProcessInfo, svcGetResourceLimitLimitValue, svcGetResourceLimitCurrentValue, svcGetProcessInfo, svcCreateResourceLimit, svcSetResourceLimitLimitValue.
* IPC improvements and fixes.
* Added envGetLastLoadResult().

#### improve usbComms support
* Allow using multiple interfaces.
* Added usbComms Ex funcs.
* Use RwLock.

#### buildsystem
* Add rules for building .npdm, .nsp and .kip.
  * Now building .npdm from .json if specified
  * .pfs0 now embeds previously built npdm if available
  * .nsp accepted as an alternative file extension to .pfs0
  * .kip can be built from elf and json descriptor (subset of .npdm)
* Added impl for accountProfile\*. If the inital smGetService fails, attempt to use 'acc:u0'.
* Added nsGetApplicationControlData. Imported nacp.h from nx-hbmenu with adjustments.
* Add ipcAddSendSmart, ipcAddRecvSmart, use where applicable
* Audio input implementation and audio output fixes.
* add portlibs bin folder to path 

#### miscellaneous
* Detect 5.0.0 properly.
* Added pmshell init/exit and pmshellLaunchProcess.
* Introduce atomics
  * atomicIncrement32
  * atomicDecrement32
  * atomicIncrement64
  * atomicDecrement64
* Added nacpGetLanguageEntry and SetLanguage_Total.
* [irs] Name image transfer config variables
* Further improvements to overall system stability and other minor adjustments to enhance the user experience.
 
## Version 1.1.0

* Fixed a race condition in HID causing sporadic incorrect key-releases when using hidKeysHeld().
* Unix socket API is now supported.
* Time support, currently only UTC.
* Added hidMouseRead().
* Added settings-services support.
* Added gfxSetDrawFlip() and gfxConfigureTransform().
* Proper (libnx-side) RomFS support. Initial fsStorage support / other fs(dev) changes.
* The console font is now 16x16.
* Fixed args parsing with quotes.
* Various audio adjustments + added audoutWaitPlayFinish().
* More irs (irsensor) support.
* Added usleep().
* General system stability improvements to enhance the user's experience.
