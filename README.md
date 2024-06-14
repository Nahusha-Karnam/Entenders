# Advanced Easy to use Burgler Alarm
## Introduction
In the age of modern IoT devices, CCTV cameras are commonly used for surveillance. However, they are often difficult to install, require internet connectivity, consume significant memory, and can't be installed in private rooms where privacy is a concern. Therefore, there is a need for a medium-level security device that can detect trespassing, is easy to install, and operates with minimal power.

## Overview
The Advanced Easy to Use Burglar Alarm uses an ultrasonic radar sensor to detect any object passing through its field of view. It is equipped with a passive buzzer that alerts the user whenever an intrusion is detected. While similar functionality can be achieved with a laser detection system, where a laser is pointed at a Light Dependent Resistor (LDR) and detects trespassing when its line of sight is blocked, such systems require extensive setup and wiring and are not foolproof.

In contrast, the Advanced Easy to Use Burglar Alarm is designed for easy installation. It only needs to be placed perpendicular to a solid surface. One of the key features of this device is its adaptability through the auto-adjust feature. When placed within 0.1 - 4 meters from a solid surface and turned on, the device's LED lights up, during which it measures the distance to the solid surface and sets its threshold. After the LED turns off, the device is ready to detect any object passing through its field of view and alerts the user with its buzzer, and just requires 5V DC power which can be provided with a 5V DC adapter or a battery bank.

## Key Features
- Easy Installation: Requires minimal setup; simply place it perpendicular to a solid surface, with 5v DC connection.
- Auto-Adjust Feature: Automatically calibrates the detection threshold within 10 seconds of being turned on.
- Adaptable Range: Can be placed between 0.1- 4 meters from the detection surface.
- Low Power Consumption: Designed to operate efficiently with just 5V DC power which can be provided from a 5V adapter or a Battery bank.
- Privacy-Friendly: Suitable for use in private rooms without violating privacy.

## Components Required
- VSD Squadron Mini developement board
- Male USB C Cable
- HC-SR04 Ultrasonic Sensor
- Bread Board
- Male to Male; Male to Female jumper cable
- Red LED
- Passive Buzzer
- 220 Ohm Resistor
- Toggle Switch


## Circuit Diagram

<img width="801" alt="circuit" src="https://github.com/Nahusha-Karnam/Entenders/assets/171113429/32583990-2f75-4ef2-9e9b-5ec50bb0ab2b">

## Table for Pin Connection
| HC-SR04 Ultrasonic Sensor | VSD Squadron Mini |
|---------------------------|-------------------|
| VCC | 5V |
| Trig | PD3 |
| Echo | PD2 |
| Gnd | Gnd |



| LED | Resistor | VSD Squadron Mini|
|-----|------|------------------|
| + |  | PD4 |
| - | 220Ω | Gnd|


| Buzzer | VSD Squadron Mini|
|--------|------------------|
| Pin 1 | PC7 |
| Pin 2 | Gnd|

| Button Switch |  VSD Squadron Mini |
|---------------|--------------------|
| Pin 1 | 5V |
| Pin 2 | PC3 |

#include "debug.h"

uint16_t distance;
uint16_t press;

void Input_Capture_Init(uint16_t arr, uint32_t psc)
{
    GPIO_InitTypeDef        GPIO_InitStructure = {0};
    TIM_ICInitTypeDef       TIM_ICInitStructure = {0};
    TIM_TimeBaseInitTypeDef TIM_TimeBaseInitStructure = {0};
    NVIC_InitTypeDef        NVIC_InitStructure = {0};

    RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOD | RCC_APB2Periph_GPIOC | RCC_APB2Periph_TIM1, ENABLE);

    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_2;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IPD;
    GPIO_Init(GPIOD, &GPIO_InitStructure);
    GPIO_ResetBits(GPIOD, GPIO_Pin_2);

    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_3;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IPD;
    GPIO_Init(GPIOC, &GPIO_InitStructure);

    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_3 | GPIO_Pin_4;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
    GPIO_Init(GPIOD, &GPIO_InitStructure);

    GPIO_InitStructure.GPIO_Pin = GPIO_Pin_7;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
    GPIO_Init(GPIOC, &GPIO_InitStructure);

    TIM_TimeBaseInitStructure.TIM_Period = arr;
    TIM_TimeBaseInitStructure.TIM_Prescaler = psc;
    TIM_TimeBaseInitStructure.TIM_ClockDivision = TIM_CKD_DIV1;
    TIM_TimeBaseInitStructure.TIM_CounterMode = TIM_CounterMode_Up;
    TIM_TimeBaseInitStructure.TIM_RepetitionCounter = 0x00;
    TIM_TimeBaseInit(TIM1, &TIM_TimeBaseInitStructure);

    TIM_ICInitStructure.TIM_Channel = TIM_Channel_1;
    TIM_ICInitStructure.TIM_ICPrescaler = TIM_ICPSC_DIV1;
    TIM_ICInitStructure.TIM_ICFilter = 0x00;
    TIM_ICInitStructure.TIM_ICPolarity = TIM_ICPolarity_Rising;
    TIM_ICInitStructure.TIM_ICSelection = TIM_ICSelection_DirectTI;

    TIM_PWMIConfig(TIM1, &TIM_ICInitStructure);

    NVIC_InitStructure.NVIC_IRQChannel = TIM1_CC_IRQn;
    NVIC_InitStructure.NVIC_IRQChannelPreemptionPriority = 0;
    NVIC_InitStructure.NVIC_IRQChannelSubPriority = 1;
    NVIC_InitStructure.NVIC_IRQChannelCmd = ENABLE;
    NVIC_Init(&NVIC_InitStructure);

    TIM_ITConfig(TIM1, TIM_IT_CC1 | TIM_IT_CC2, ENABLE);

    TIM_SelectInputTrigger(TIM1, TIM_TS_TI1FP1);
    TIM_SelectSlaveMode(TIM1, TIM_SlaveMode_Reset);
    TIM_SelectMasterSlaveMode(TIM1, TIM_MasterSlaveMode_Enable);
    TIM_Cmd(TIM1, ENABLE);
}

uint16_t pressed(void){
    if(GPIO_ReadInputDataBit(GPIOC,GPIO_Pin_3)==1){
        Delay_Ms(500);
        GPIO_WriteBit(GPIOC,GPIO_Pin_7,SET);
        Delay_Ms(100);
        GPIO_WriteBit(GPIOC,GPIO_Pin_7,RESET);
        Delay_Ms(1000);
        press=!press;
    }
    return press;
}

int main(void)
{
    SystemCoreClockUpdate();
    Delay_Init();
    USART_Printf_Init(115200);
    Input_Capture_Init(0xFFFF, 48 - 1);
    uint32_t count=0;
    uint32_t value=0;
    uint16_t avg=0;
    
    while (pressed())
    {     
        GPIO_WriteBit(GPIOD, GPIO_Pin_3, SET);
        Delay_Us(10); 
        GPIO_WriteBit(GPIOD, GPIO_Pin_3, RESET);
        if(count<=4000){
            count+=1;
            GPIO_WriteBit(GPIOD,GPIO_Pin_4,SET);
            value+=distance;
            Delay_Ms(1);
        }else if(count==4001){
            avg = value/count;
            GPIO_WriteBit(GPIOC,GPIO_Pin_7,SET);
            Delay_Ms(100);
            GPIO_WriteBit(GPIOC,GPIO_Pin_7,RESET);
            Delay_Ms(100);
            GPIO_WriteBit(GPIOC,GPIO_Pin_7,SET);
            Delay_Ms(100);
            GPIO_WriteBit(GPIOC,GPIO_Pin_7,RESET);
            Delay_Ms(100);
            count+=1;
        }else if(count>4001 && count<4050){
            count+=1;
            Delay_Ms(1);
        }else{
            GPIO_WriteBit(GPIOD,GPIO_Pin_4,RESET);
            if(distance<avg-10 || distance>avg+10){
                count=0;
                while(pressed()){
                    GPIO_WriteBit(GPIOC,GPIO_Pin_7,SET);
                    GPIO_WriteBit(GPIOD,GPIO_Pin_4,SET);
                    Delay_Ms(500);
                    GPIO_WriteBit(GPIOC,GPIO_Pin_7,RESET);
                    GPIO_WriteBit(GPIOD,GPIO_Pin_4,RESET);
                    Delay_Ms(500);
                }
            }
        }  
    }
}



void TIM1_CC_IRQHandler(void) __attribute__((interrupt("WCH-Interrupt-fast")));

void TIM1_CC_IRQHandler(void)
{
    if (TIM_GetITStatus(TIM1, TIM_IT_CC1) != RESET)
    {
        TIM_SetCounter(TIM1,0);
    }

    if (TIM_GetITStatus(TIM1, TIM_IT_CC2) != RESET)
    {
        uint32_t duration = TIM_GetCapture1(TIM1);
        distance = duration*0.034/2;
        printf("%d\n",distance);
        
    }

    TIM_ClearITPendingBit(TIM1, TIM_IT_CC1 | TIM_IT_CC2);
}

https://github.com/Nahusha-Karnam/Entenders/assets/171113429/6c15f4de-d980-409a-8bc6-57afdc2adde6

