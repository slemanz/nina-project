CC=arm-none-eabi-gcc
MACH=cortex-m4
LINKER_SCRIPT=linkers/nrf52.ld
TARGET=flash


CFLAGS= -c -mcpu=$(MACH) -mthumb -mfloat-abi=soft -std=gnu11 -Wall -O0 
LDFLAGS =  -mcpu=$(MACH) -mthumb -mfloat-abi=soft --specs=nano.specs -T $(LINKER_SCRIPT) -Wl,-Map=app/Build/$(TARGET).map
OBJCOPY=arm-none-eabi-objcopy

###########################################
#				 INCLUDES

INCLUDES+= -I app/Inc/
INCLUDES+= -I drivers/Inc/ 
INCLUDES+= -I common/Inc/ 
INCLUDES+= -I shared/Inc/



############################################
# Source files

OBJS		+= app/Build/main.o
OBJS		+= app/Build/init_app.o
OBJS		+= app/Build/nrf52_startup.o
OBJS		+= app/Build/syscalls.o

OBJS		+= app/Build/driver_event.o
OBJS		+= app/Build/driver_interrupt.o
OBJS		+= app/Build/driver_gpio.o
OBJS		+= app/Build/driver_systick.o
OBJS		+= app/Build/driver_uart.o
OBJS		+= app/Build/driver_spi.o
OBJS		+= app/Build/driver_twi.o
OBJS		+= app/Build/driver_saadc.o

BSP 		+= app/Build/nor.o
BSP 		+= app/Build/temperature.o
BSP 		+= app/Build/led_rgb.o

OBJS		+= app/Build/cli.o
OBJS		+= app/Build/storage.o
OBJS		+= app/Build/ticks.o
OBJS		+= app/Build/status.o

COMMON 		+= app/Build/ring-buffer.o



all: app/Build/$(TARGET).elf

############################################
# Build rules

app/Build/%.o: Src/%.c
	$(CC) $(CFLAGS) $(INCLUDES) -o app/Build/$(*).o Src/$(*).c

app/Build/%.o: app/Src/%.c
	$(CC) $(CFLAGS) $(INCLUDES) -o app/Build/$(*).o app/Src/$(*).c

app/Build/%.o: linkers/%.c
	$(CC) $(CFLAGS) $(INCLUDES) -o app/Build/$(*).o linkers/$(*).c

app/Build/%.o: drivers/Src/%.c
	$(CC) $(CFLAGS) $(INCLUDES) -o app/Build/$(*).o drivers/Src/$(*).c

app/Build/%.o: common/Src/bsp/%.c
	$(CC) $(CFLAGS) $(INCLUDES) -o app/Build/$(*).o common/Src/bsp/$(*).c

app/Build/%.o: common/Src/core/%.c
	$(CC) $(CFLAGS) $(INCLUDES) -o app/Build/$(*).o common/Src/core/$(*).c

app/Build/%.o: shared/Src/%.c
	$(CC) $(CFLAGS) $(INCLUDES) -o app/Build/$(*).o shared/Src/$(*).c

############################################
# Finals

app/Build/$(TARGET).elf: $(OBJS) $(COMMON) $(BSP)
	$(CC) $(LDFLAGS) -o $@ $^
	$(OBJCOPY) -O binary app/Build/$(TARGET).elf app/Build/$(TARGET).bin

load: 
	openocd -f interface/jlink.cfg -c "transport select swd" -f target/nrf52.cfg -c init -c "reset init" \
	-c "flash write_image erase app/Build/$(TARGET).elf" -c "reset run" -c shutdown

reset:
	openocd -f interface/jlink.cfg -c "transport select swd" -f target/nrf52.cfg -c init -c "reset run" -c shutdown

clean:
	rm -rf  *.map app/Build/*.o