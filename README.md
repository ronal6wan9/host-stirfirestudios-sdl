# host-stirfirestudios-sdl
The Stirfire Studios SDL host
Build using Moai 1.6.0-stable

BUILD INSTRUCTION:
- 1. Clone/download and extract Moai-1.6.0-stable (use the source sdk, not binary sdk)
- 2. Copy CMakeLists.txt under cmake folder to Moai-1.6.0-stable\cmake\hosts\host-stirfire
- 3. Copy build-stirfire-win.bat under bin folder to Moai-1.6.0-stable\bin
- 4. Copy husky, moai-husky, sledge folders to Moai-1.6.0-stable\bin\host-stirfire
- 5. Change working directory to Moai-1.6.0-stable and run bin\build-stirfire-win.bat [arg1] [arg2] [arg3]

NOTE:   
- arg1 = vs version (default to vs2013)
- arg2 = output folder (default to Moai-1.6.0-stable\lib\stirfire\vs2013)
- arg3 = host folder, if you put 2 & 4 under name other than host-stirfire specify here
  
TODO:
- further integration with moaiutil 
- more testing (managed to load my title scene just fine)
- still doesn't work under Zerobrane "Start or continue debugging" only under "Execute the current project/file"
