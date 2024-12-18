# SSIHL ROADSHOW
***Repository on RISC-V and VLSI Chip Design Roadshow at SSSIHL***
***

# OVERVIEW OF THE WORKSHOP

***The one-day workshop provided a comprehensive introduction to VLSI and RISC-V, covering the chip design process and various microprocessor types. It enhanced our understanding of semiconductor technologies and opened new career opportunities in chip design and microelectronics.***
# PROCEDURE
#  Installed Oracle VirtualBox  successfully by adding vsdsquadron

![Screenshot 2024-12-13 100230](https://github.com/user-attachments/assets/0890ae9e-4528-42d5-917d-61f79931e2fb)
 ***The workshop introduced us to VSD (VLSI System Design) Quadron and VirtualBox. We explored how VSD Quadron supports chip design and simulation***
 ***
# Installed gedit successfully, made a file vsd.c and ran a code to add numbers from 1 to 10 
![Screenshot 2024-12-13 102620](https://github.com/user-attachments/assets/4da27d47-542d-4739-b63f-72306d83d9c0)

```sudo apt install gedit``` ***WAS THE CODE USED***
```
#include<stdio.h>
  int main(){
    int sum = 0, i, n;
    printf("Enter the value of n = ");
    scanf("%d",&n);
    for(i = 1;i <= n;i++){
       sum = sum + i;
    }
    printf("The Sum of numbers from 1 to %d is %d\n",n,sum);
    return 0;
}
```
***
# Compiling the vardan.c C program for RISC-V architecture using riscv64-unknown-elf-gcc
  ![Screenshot 2024-12-13 105312](https://github.com/user-attachments/assets/3c4c3cdf-ecaa-45fe-85a8-3cd802d1146d)

  ```riscv64-unknown-elf-gcc -O1 -mabi=lp64 -march=rv64i -o {filename}.o {filename}.c` and iscv64-unknown-elf-objdump -d {filename}.o ```
  ***was used and following result was obtained***
  ![image](https://github.com/user-attachments/assets/977de7f4-57b1-4b2d-be00-81ae655fe90f)
***
# Now openlane,docker,./flow.tcl -interactive was used
(```openlane,docker,./flow.tcl -interactive```)
![Screenshot 2024-12-13 121720](https://github.com/user-attachments/assets/4e9caf6a-38dc-47fb-be1a-c51eb9d1c47e)
***
# RAN PACKAGES
  ![Screenshot 2024-12-13 122431](https://github.com/user-attachments/assets/8dd1d458-3205-4429-9e2b-db56c5b550fe)
   (Ran a synthesis"run_synthesis" and was successful)
***HERE IS THE INFO OF NUMBER OF CELLS***
  ![Screenshot 2024-12-13 122938](https://github.com/user-attachments/assets/f9a937df-216b-42a5-b975-669c2ff688d8)
***
# Ran a code run_floorplane and got a png
```picorv32a.floorplan.def.png ```

[Screenshot 2024-12-13 140754](https://github.com/user-attachments/assets/c2a1b410-db95-44ad-8d45-4b0dad2fbe51)
***
# Ran code "run_placement" and got png

![Screenshot 2024-12-13 145014](https://github.com/user-attachments/assets/2f371b55-f1d2-4c04-938e-cb8d4934e339)
***
# Ran_cts
![Screenshot 2024-12-13 145721](https://github.com/user-attachments/assets/fe51a52c-cfc3-4326-bf59-1203764046d8)
***
# ARCHITECTURE OF RISC V 
![image (1)](https://github.com/user-attachments/assets/031cedbb-2836-48a0-92c6-e0ba79b9681d)
***
# Finished routing

![rt](https://github.com/user-attachments/assets/80bed00e-7f62-401c-8806-3394ca071bb3)

***(---------------------------------------------------------------------------------)***
``` NOW WE CODED THE CHIP ```
# BLINKING THE CHIP


https://github.com/user-attachments/assets/78abe9d5-975a-49e3-800e-abd047299b6c


``` #include <ch32v00x.h>
#include <debug.h>

#define BLINKY_GPIO_PORT GPIOD
#define BLINKY_GPIO_PIN GPIO_Pin_6
#define BLINKY_CLOCK_ENABLE RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOD, ENABLE)

void NMI_Handler(void) __attribute__((interrupt("WCH-Interrupt-fast")));
void HardFault_Handler(void) __attribute__((interrupt("WCH-Interrupt-fast")));
void Delay_Init(void);
void Delay_Ms(uint32_t n);

int main(void)
{
	NVIC_PriorityGroupConfig(NVIC_PriorityGroup_1);
	SystemCoreClockUpdate();
	Delay_Init();

	GPIO_InitTypeDef GPIO_InitStructure = {0};

	BLINKY_CLOCK_ENABLE;
	GPIO_InitStructure.GPIO_Pin = BLINKY_GPIO_PIN;
	GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;
	GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
	GPIO_Init(BLINKY_GPIO_PORT, &GPIO_InitStructure);

	uint8_t ledState = 0;
	while (1)
	{
		GPIO_WriteBit(BLINKY_GPIO_PORT, BLINKY_GPIO_PIN, ledState);
		ledState ^= 1; // invert for the next run
		Delay_Ms(100);
	}
}

void NMI_Handler(void) {}
void HardFault_Handler(void)
{
	while (1)
	{
	}
}
 ```
AND 

``` #include "debug.h"
#define TIME_PERIOD 1000
#define PRESC       0
#define PULSE       632
#define STEP_SIZE   10
volatile u16 val;
volatile u8 dir;
void TIM1_PWMOut_Init(void){
    GPIO_InitTypeDef GPIO_InitStructure={0};
    TIM_OCInitTypeDef TIM_OCInitStructure={0};
    TIM_TimeBaseInitTypeDef TIM_TimeBaseInitStructure={0};
    //RCC_APB2PeriphClockCmd( RCC_APB2Periph_GPIOC | RCC_APB2Periph_TIM1, ENABLE );
    RCC_APB2PeriphClockCmd( RCC_APB2Periph_GPIOD | RCC_APB2Periph_AFIO, ENABLE );
    RCC_APB1PeriphClockCmd( RCC_APB1Periph_TIM2, ENABLE );
    GPIO_PinRemapConfig(GPIO_FullRemap_TIM2, ENABLE);
    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_6;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_AF_PP;
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
    GPIO_Init( GPIOD, &GPIO_InitStructure);
    TIM_TimeBaseInitStructure.TIM_Period = TIME_PERIOD;
    TIM_TimeBaseInitStructure.TIM_Prescaler = PRESC;
    TIM_TimeBaseInitStructure.TIM_ClockDivision = TIM_CKD_DIV1;
    TIM_TimeBaseInitStructure.TIM_CounterMode = TIM_CounterMode_Up;
    TIM_TimeBaseInit( TIM2, &TIM_TimeBaseInitStructure);
    TIM_OCInitStructure.TIM_OCMode = TIM_OCMode_PWM2;
    TIM_OCInitStructure.TIM_OutputState = TIM_OutputState_Enable;
    TIM_OCInitStructure.TIM_Pulse = PULSE;
    TIM_OCInitStructure.TIM_OCPolarity = TIM_OCPolarity_High;
    TIM_OC3Init( TIM2, &TIM_OCInitStructure );
    TIM_CtrlPWMOutputs(TIM2, ENABLE );
    //TIM_OC3PreloadConfig( TIM1, TIM_OCPreload_Disable );
    //TIM_ARRPreloadConfig( TIM1, ENABLE );
    TIM_Cmd( TIM2, ENABLE );
}
int main(void)
{
    NVIC_PriorityGroupConfig(NVIC_PriorityGroup_1);
    SystemCoreClockUpdate();
    Delay_Init();
    TIM1_PWMOut_Init();
    val = 0;
    dir = 0;
    // Loop
    while(1){
        val += (dir) ? -STEP_SIZE : STEP_SIZE;
        TIM_SetCompare3(TIM2, val);
        dir ^= (val == 1000 || val == 0) ? 1 : 0;
        Delay_Ms(15);
    }
}
```
***
**THE ABOVE VIDEO IS THE PARCTICAL VISUAL OF THE CHIP WITH THAT CODE**

___

**I would like to express my sincere gratitude for the insightful workshop on VLSI and RISC-V,
which significantly deepened my understanding of chip design and related technologies. The session on VSD Quadron and VirtualBox was particularly valuable,
and it has further fueled my interest in pursuing a career in VLSI and microelectronics.**
<hr style="border: 3px solid black;">







  
  

 

  




