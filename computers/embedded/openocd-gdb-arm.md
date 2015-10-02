configuring OpenOCD and gdb for ARM
===================================

OpenOCD (Open On Chip Debugger) is an open-source hardware debugging tool.

openocd --file board/ek-lm3s811.cfg
telnet localhost 4444
gdb target remote localhost:3333
mdw 0 16        ## prints out 16 words from flash memory
flash info 0    ## lists out everything about flash bank 0
halt            ## halt processor for programming (can't be erased otherwise)
flash probe 0   ## looks for flash and reports address mapped to
## openocd read manual
## dirac.org gdb manual
## write telnet output to file
monitor reset halt
flash write_image [erase] [unlock] <abs/path/to/file> [offset] [filetype]

inside telnet: reg lists all registers
