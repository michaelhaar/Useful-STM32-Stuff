# Source File Documentation

## Why Documentation is needed...
If you write a good documentation, you'll be able to remember everything
even after two weeks of vacation and partying ;)

## How to write documentation for C-Code
There's a framework called [Doxygen](http://www.doxygen.nl/) for generation
documentation from annotated C files. Simply write your comments as demonstrated
on the [homepage](http://www.doxygen.nl/manual/docblocks.html).

## Ingredients for a good source file documentation

### File Header
Every well documented C-file should have a file header.

It should include at least the `@file`, `@author` and the `@brief` commands.
A detailed description with some usage examples is also very helpful.
Also add a copyright and license information if necessary.

Here's an example from the STM32 hardware abstraction layer (HAL):
```
/**
  ******************************************************************************
  * @file    stm32f0xx_hal_spi.c
  * @author  MCD Application Team
  * @brief   SPI HAL module driver.
  *          This file provides firmware functions to manage the following
  *          functionalities of the Serial Peripheral Interface (SPI) peripheral:
  *           + Initialization and de-initialization functions
  *           + IO operation functions
  *           + Peripheral Control functions
  *           + Peripheral State functions
  *
  @verbatim
  ==============================================================================
                        ##### How to use this driver #####
  ==============================================================================
    [..]
      The SPI HAL driver can be used as follows:

      (#) Declare a SPI_HandleTypeDef handle structure, for example:
          SPI_HandleTypeDef  hspi;

      (#)Initialize the SPI low level resources by implementing the HAL_SPI_MspInit() API:
          (##) Enable the SPIx interface clock
          (##) SPI pins configuration
              (+++) Enable the clock for the SPI GPIOs
              (+++) Configure these SPI pins as alternate function push-pull
          (##) NVIC configuration if you need to use interrupt process
              (+++) Configure the SPIx interrupt priority
              (+++) Enable the NVIC SPI IRQ handle
          (##) DMA Configuration if you need to use DMA process
              (+++) Declare a DMA_HandleTypeDef handle structure for the transmit or receive Stream/Channel
              (+++) Enable the DMAx clock
              (+++) Configure the DMA handle parameters
              (+++) Configure the DMA Tx or Rx Stream/Channel
              (+++) Associate the initialized hdma_tx handle to the hspi DMA Tx or Rx handle
              (+++) Configure the priority and enable the NVIC for the transfer complete interrupt on the DMA Tx or Rx Stream/Channel

      (#) Program the Mode, BidirectionalMode , Data size, Baudrate Prescaler, NSS
          management, Clock polarity and phase, FirstBit and CRC configuration in the hspi Init structure.

      (#) Initialize the SPI registers by calling the HAL_SPI_Init() API:
          (++) This API configures also the low level Hardware GPIO, CLOCK, CORTEX...etc)
              by calling the customized HAL_SPI_MspInit() API.
     [..]
       Circular mode restriction:
      (#) The DMA circular mode cannot be used when the SPI is configured in these modes:
          (##) Master 2Lines RxOnly
          (##) Master 1Line Rx
      (#) The CRC feature is not managed when the DMA circular mode is enabled
      (#) When the SPI DMA Pause/Stop features are used, we must use the following APIs
          the HAL_SPI_DMAPause()/ HAL_SPI_DMAStop() only under the SPI callbacks
     [..]
       Master Receive mode restriction:
      (#) In Master unidirectional receive-only mode (MSTR =1, BIDIMODE=0, RXONLY=0) or
          bidirectional receive mode (MSTR=1, BIDIMODE=1, BIDIOE=0), to ensure that the SPI
          does not initiate a new transfer the following procedure has to be respected:
          (##) HAL_SPI_DeInit()
          (##) HAL_SPI_Init()
     [..]
       The HAL drivers do not allow reaching all supported SPI frequencies in the different SPI
       modes. Refer to the source code (stm32xxxx_hal_spi.c header) to get a summary of the
       maximum SPI frequency that can be reached with a data size of 8 or 16 bits, depending on
       the APBx peripheral clock frequency (fPCLK) used by the SPI instance.

     [..]
       Data buffer address alignment restriction:
      (#) In case more than 1 byte is requested to be transferred, the HAL SPI uses 16-bit access for data buffer.
          But there is no support for unaligned accesses on the Cortex-M0 processor.
          So, if the user wants to transfer more than 1 byte, it shall ensure that 16-bit aligned address is used for:
          (##) pData parameter in HAL_SPI_Transmit(), HAL_SPI_Transmit_IT(), HAL_SPI_Receive() and HAL_SPI_Receive_IT()
          (##) pTxData and pRxData parameters in HAL_SPI_TransmitReceive() and HAL_SPI_TransmitReceive_IT()
      (#) There is no such restriction when going through DMA by using HAL_SPI_Transmit_DMA(), HAL_SPI_Receive_DMA()
          and HAL_SPI_TransmitReceive_DMA().

  @endverbatim

  Additional table :

       DataSize = SPI_DATASIZE_8BIT:
       +----------------------------------------------------------------------------------------------+
       |         |                | 2Lines Fullduplex   |     2Lines RxOnly    |         1Line        |
       | Process | Tranfert mode  |---------------------|----------------------|----------------------|
       |         |                |  Master  |  Slave   |  Master   |  Slave   |  Master   |  Slave   |
       |==============================================================================================|
       |    T    |     Polling    | Fpclk/4  | Fpclk/8  |    NA     |    NA    |    NA     |   NA     |
       |    X    |----------------|----------|----------|-----------|----------|-----------|----------|
       |    /    |     Interrupt  | Fpclk/4  | Fpclk/16 |    NA     |    NA    |    NA     |   NA     |
       |    R    |----------------|----------|----------|-----------|----------|-----------|----------|
       |    X    |       DMA      | Fpclk/2  | Fpclk/2  |    NA     |    NA    |    NA     |   NA     |
       |=========|================|==========|==========|===========|==========|===========|==========|
       |         |     Polling    | Fpclk/4  | Fpclk/8  | Fpclk/16  | Fpclk/8  | Fpclk/8   | Fpclk/8  |
       |         |----------------|----------|----------|-----------|----------|-----------|----------|
       |    R    |     Interrupt  | Fpclk/8  | Fpclk/16 | Fpclk/8   | Fpclk/8  | Fpclk/8   | Fpclk/4  |
       |    X    |----------------|----------|----------|-----------|----------|-----------|----------|
       |         |       DMA      | Fpclk/4  | Fpclk/2  | Fpclk/2   | Fpclk/16 | Fpclk/2   | Fpclk/16 |
       |=========|================|==========|==========|===========|==========|===========|==========|
       |         |     Polling    | Fpclk/8  | Fpclk/2  |     NA    |    NA    | Fpclk/8   | Fpclk/8  |
       |         |----------------|----------|----------|-----------|----------|-----------|----------|
       |    T    |     Interrupt  | Fpclk/2  | Fpclk/4  |     NA    |    NA    | Fpclk/16  | Fpclk/8  |
       |    X    |----------------|----------|----------|-----------|----------|-----------|----------|
       |         |       DMA      | Fpclk/2  | Fpclk/2  |     NA    |    NA    | Fpclk/8   | Fpclk/16 |
       +----------------------------------------------------------------------------------------------+

       DataSize = SPI_DATASIZE_16BIT:
       +----------------------------------------------------------------------------------------------+
       |         |                | 2Lines Fullduplex   |     2Lines RxOnly    |         1Line        |
       | Process | Tranfert mode  |---------------------|----------------------|----------------------|
       |         |                |  Master  |  Slave   |  Master   |  Slave   |  Master   |  Slave   |
       |==============================================================================================|
       |    T    |     Polling    | Fpclk/4  | Fpclk/8  |    NA     |    NA    |    NA     |   NA     |
       |    X    |----------------|----------|----------|-----------|----------|-----------|----------|
       |    /    |     Interrupt  | Fpclk/4  | Fpclk/16 |    NA     |    NA    |    NA     |   NA     |
       |    R    |----------------|----------|----------|-----------|----------|-----------|----------|
       |    X    |       DMA      | Fpclk/2  | Fpclk/2  |    NA     |    NA    |    NA     |   NA     |
       |=========|================|==========|==========|===========|==========|===========|==========|
       |         |     Polling    | Fpclk/4  | Fpclk/8  | Fpclk/16  | Fpclk/8  | Fpclk/8   | Fpclk/8  |
       |         |----------------|----------|----------|-----------|----------|-----------|----------|
       |    R    |     Interrupt  | Fpclk/8  | Fpclk/16 | Fpclk/8   | Fpclk/8  | Fpclk/8   | Fpclk/4  |
       |    X    |----------------|----------|----------|-----------|----------|-----------|----------|
       |         |       DMA      | Fpclk/4  | Fpclk/2  | Fpclk/2   | Fpclk/16 | Fpclk/2   | Fpclk/16 |
       |=========|================|==========|==========|===========|==========|===========|==========|
       |         |     Polling    | Fpclk/8  | Fpclk/2  |     NA    |    NA    | Fpclk/8   | Fpclk/8  |
       |         |----------------|----------|----------|-----------|----------|-----------|----------|
       |    T    |     Interrupt  | Fpclk/2  | Fpclk/4  |     NA    |    NA    | Fpclk/16  | Fpclk/8  |
       |    X    |----------------|----------|----------|-----------|----------|-----------|----------|
       |         |       DMA      | Fpclk/2  | Fpclk/2  |     NA    |    NA    | Fpclk/8   | Fpclk/16 |
       +----------------------------------------------------------------------------------------------+
       @note The max SPI frequency depend on SPI data size (4bits, 5bits,..., 8bits,...15bits, 16bits),
             SPI mode(2 Lines fullduplex, 2 lines RxOnly, 1 line TX/RX) and Process mode (Polling, IT, DMA).
       @note
            (#) TX/RX processes are HAL_SPI_TransmitReceive(), HAL_SPI_TransmitReceive_IT() and HAL_SPI_TransmitReceive_DMA()
            (#) RX processes are HAL_SPI_Receive(), HAL_SPI_Receive_IT() and HAL_SPI_Receive_DMA()
            (#) TX processes are HAL_SPI_Transmit(), HAL_SPI_Transmit_IT() and HAL_SPI_Transmit_DMA()

  ******************************************************************************
  * @attention
  *
  * <h2><center>&copy; COPYRIGHT(c) 2016 STMicroelectronics</center></h2>
  *
  * Redistribution and use in source and binary forms, with or without modification,
  * are permitted provided that the following conditions are met:
  *   1. Redistributions of source code must retain the above copyright notice,
  *      this list of conditions and the following disclaimer.
  *   2. Redistributions in binary form must reproduce the above copyright notice,
  *      this list of conditions and the following disclaimer in the documentation
  *      and/or other materials provided with the distribution.
  *   3. Neither the name of STMicroelectronics nor the names of its contributors
  *      may be used to endorse or promote products derived from this software
  *      without specific prior written permission.
  *
  * THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
  * AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
  * IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
  * DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE
  * FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL
  * DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR
  * SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER
  * CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY,
  * OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
  * OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
  *
  ******************************************************************************
  */
```


### Function Documentation
Each function should be documented and provide:
- a brief function overview.
- detailed description of the function.
- detailed parameter explanation.
- detailed information about return values.

Function comments usually look like this:
```
/**
 * @brief  Enable Interrupt in NVIC Interrupt Controller
 * @param  IRQn  interrupt number that specifies the interrupt
 * @return none.
 * Enable the specified interrupt in the NVIC Interrupt Controller.
 * Other settings of the interrupt such as priority are not affected.
 */
```


### Doxygen groups
Functions should be grouped into [Doxygen Groups](http://www.doxygen.nl/manual/grouping.html).

The first argument of the command is a label that should uniquely identify the group.
The second argument is the name or title of the group as it should appear in the documentation. You can also add a brief and detailed description if you want.

```c
/** @defgroup SPI_Exported_Functions_Group1 Initialization and de-initialization functions
 *  @brief    Initialization and Configuration functions
 *
@verbatim
 ===============================================================================
              ##### Initialization and de-initialization functions #####
 ===============================================================================
    [..]  This subsection provides a set of functions allowing to initialize and
          de-initialize the SPIx peripheral:

      (+) User must implement HAL_SPI_MspInit() function in which he configures
          all related peripherals resources (CLOCK, GPIO, DMA, IT and NVIC ).

      (+) Call the function HAL_SPI_Init() to configure the selected device with
          the selected configuration:
        (++) Mode
        (++) Direction
        (++) Data Size
        (++) Clock Polarity and Phase
        (++) NSS Management
        (++) BaudRate Prescaler
        (++) FirstBit
        (++) TIMode
        (++) CRC Calculation
        (++) CRC Polynomial if CRC enabled
        (++) CRC Length, used only with Data8 and Data16
        (++) FIFO reception threshold

      (+) Call the function HAL_SPI_DeInit() to restore the default configuration
          of the selected SPIx peripheral.

@endverbatim
  * @{
  */


HAL_StatusTypeDef HAL_SPI_Init(SPI_HandleTypeDef *hspi)
{
    //do something
}


HAL_StatusTypeDef HAL_SPI_DeInit(SPI_HandleTypeDef *hspi)
{
    // do something
}


__weak void HAL_SPI_MspInit(SPI_HandleTypeDef *hspi)
{
  // do something
}


__weak void HAL_SPI_MspDeInit(SPI_HandleTypeDef *hspi)
{
  // do something
}


/**
  * @}
  */
```


### Structure of a source file
The source files should use the following structure:
```
// File header as described above.

/* Includes ------------------------------------------------------------------*/

/* Private typedef -----------------------------------------------------------*/

/* Private defines -----------------------------------------------------------*/

/* Private macros ------------------------------------------------------------*/

/* Private variables ---------------------------------------------------------*/

/* Private function prototypes -----------------------------------------------*/

/* Exported functions --------------------------------------------------------*/
```
