---
title: Tekniska specifikationer och efterlevnad för Microsoft Azure Stack Edge Pro R | Microsoft Docs
description: Lär dig mer om tekniska specifikationer och efterlevnad för din Azure Stack Edge Pro R-enhet
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: alkohli
ms.openlocfilehash: 56d301762fde41f727b0b425d6c41a423f08103c
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467293"
---
# <a name="azure-stack-edge-pro-r-technical-specifications"></a>Tekniska specifikationer för Azure Stack Edge Pro R

Maskin varu komponenterna i din Azure Stack Edge Pro R-enhet följer de tekniska specifikationer som beskrivs i den här artikeln. De tekniska specifikationerna beskriver strömförsörjnings enheter (PSUs), lagrings kapacitet, höljen och miljö standarder.


## <a name="compute-memory-specifications"></a>Beräkning, minnes krav

Azure Stack Edge Pro R-enheten har följande specifikationer för beräkning och minne:

| Specifikation       | Värde                  |
|---------------------|------------------------|
| Processor    | 2 X 10 core CPU, Intel Xeon silver 4114 |
| Minne              | 256 GB RAM-minne (2666 MT/s)     |


## <a name="compute-acceleration-specifications"></a>Specifikationer för beräknings acceleration

En GPU (Graphics Processing Unit) ingår på varje enhet som möjliggör Kubernetes, djup inlärning och maskin inlärnings scenarier.

| Specifikation           | Värde                  |
|-------------------------|----------------------------|
| GPU   | En nVidia T4-GPU <br> Mer information finns i [NVIDIA-T4](https://www.nvidia.com/en-us/data-center/tesla-t4/).| 

## <a name="power-supply-unit-specifications"></a>Specifikationer för strömförsörjnings enhet

Azure Stack Edge Pro R-enheten har två 100-240 V-enheter för strömförsörjning (PSUs) med högpresterande fläktar. De två PSUs ger en redundant energi konfiguration. Om ett PSU Miss lyckas fortsätter enheten att fungera normalt på den andra PSU tills den felaktiga modulen har ersatts. I följande tabell visas de tekniska specifikationerna för PSUs.

| Specifikation           | 550 W PSU                  |
|-------------------------|----------------------------|
| Maximal uteffekt    | 550 W                      |
| Värme avledning (max)                   | 2891 BTU/timme                |
| Frekvens               | 50/60 Hz                   |
| Val av spännings intervall | Automatisk mellan: 115-230 V AC |
| Hot pluggable           | Yes                        |

## <a name="network-specifications"></a>Nätverks specifikationer

Azure Stack Edge Pro R-enheten har fyra nätverks gränssnitt, PORT1-PORT4. 


|Specifikation  |Description                              |
|----------------------|----------------------------------|
|Nätverksgränssnitt    |**2 x 1 GbE RJ45** <br> PORT 1 används som hanterings gränssnitt för inledande installation och är statisk som standard. När den första installationen är klar kan du använda gränssnittet för data med valfri IP-adress. Men vid återställning återgår gränssnittet till statisk IP. <br>Den andra gränssnitts porten 2 är användaren konfigurerbar, kan användas för data överföring och är DHCP som standard.     |
|Nätverksgränssnitt    |**2 x 25 GbE-SFP28** <br> Dessa data gränssnitt PORT 3 och PORT 4 kan konfigureras som DHCP (standard) eller statiskt.            |

Din Azure Stack Edge Pro R-enhet har följande nätverks maskin vara:

* **Mellanox dual port 25G ConnectX – 4 kanal nätverkskort** -Port 3 och Port 4. 

<!--Here are the details for the Mellanox card: MCX4421A-ACAN

| Parameter           | Description                 |
|-------------------------|----------------------------|
| Model    | ConnectX®-4 Lx EN network interface card                      |
| Model Description               | 25GbE dual-port SFP28; PCIe3.0 x8; ROHS R6                    |
| Device Part Number (XR2) | MCX4421A-ACAN  |
| PSID (R640)           | MT_2420110034                         |-->
<!-- confirm w/ Ravi what is this-->

En fullständig lista över kablar, växlar och Sänd tagare som stöds för dessa nätverkskort finns på: [Mellanox dual port 25G ConnectX-4 Channel-kompatibla produkter](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products).

## <a name="storage-specifications"></a>Storage-specifikationer

Azure Stack Edge Pro R-enheter har 8 data diskar och 2 M. 2 SATA-diskar som fungerar som operativ system diskar. Mer information finns på [M. 2 SATA-diskar](https://en.wikipedia.org/wiki/M.2).

#### <a name="storage-for-1-node-device"></a>Lagring för 1-nod-enhet

I följande tabell finns information om lagrings kapaciteten för 1-Node-enheten.

|     Specifikation                          |     Värde             |
|--------------------------------------------|-----------------------|
|    Antal solid state-hårddiskar (SSD)     |    8                  |
|    Kapacitet för enskild SSD                     |    8 TB               |
|    Total kapacitet                          |    64 TB              |
|    Total användbar kapacitet *                  |    ~ 42 TB          |

**En del utrymme är reserverat för internt bruk.*

<!--#### Storage for 4-node device

The following table has the details for the storage capacity of the 4-node device.

|     Specification                          |     Value             |
|--------------------------------------------|-----------------------|
|    Number of solid-state drives (SSDs)     |    32 (4 X 8 disks for 4 devices)                |
|    Single SSD capacity                     |    8 TB               |
|    Total capacity                          |    256 TB              |
|    Total usable capacity*                  |    ~ 163 TB          |

**After mirroring and parity, and reserving some space for internal use.* -->


## <a name="enclosure-dimensions-and-weight-specifications"></a>Bilagans dimensioner och vikt specifikationer

I följande tabeller visas de olika specifikationerna för inne slutning för dimensioner och vikt.

### <a name="enclosure-dimensions"></a>Omslutnings dimensioner 

I följande tabell visas enhetens mått och UPS-enheten med ett robust hölje i millimeter och tum.

|     Hölje     |     Millimeter     |     Mm     |
|-------------------|---------------------|----------------|
|    Höjd         |    301,2            |    11,86       |
|    Bredd          |    604,5            |    23,80       |
|    Längd         |    740,4            |    35,50       |

<!--#### For the 4-node system

For the 4-node system, the servers and the heater are shipped in a 5U case and the UPS are shipped in a 4U case.

The following table lists the dimensions of the 5U device case:  

|     Enclosure     |     Millimeters   |     Inches     |
|-------------------|-------------------|----------------|
|    Height         |    387.4          |    15.25       |
|    Width          |    604.5          |    23.80       |
|    Length         |    901.7          |    35.50       |

The following table lists the dimensions of the 4U UPS case: 

|     Enclosure     |     Millimeters   |     Inches    |
|-------------------|-------------------|---------------|
|    Height         |    342.9          |    13.5       |
|    Width          |    604.5          |   23.80       |
|    Length         |    901.7          |   35.50       |
-->

### <a name="enclosure-weight"></a>Kabinett vikt 

Enhetens vikt beror på konfigurationen av kabinett filen.

|     Hölje                                 |     Vikt          |
|-----------------------------------------------|---------------------|
|    Total vikt på 1-nods enhet + robust hölje med slut punkter     |    ~ 114 kg.          |

<!--#### For the 4-node system

|     Enclosure                                 |     Weight          |
|-----------------------------------------------|---------------------|
|   Approximate weight of fully populated 4 devices + heater in 5U case     |    ~200 lbs.          |
|   Approximate weight of fully populated 4 UPS in 4U case    |    ~145 lbs.          |
-->

## <a name="enclosure-environment-specifications"></a>Specifikationer för kabinett miljö

I det här avsnittet visas de specifikationer som är relaterade till inne slutnings miljön, till exempel temperatur, vibrationer, stöt och höjd.


|     Specifikation              |     Värde    |
|--------------------------------|-------------------------------------------------------------------|
|     Temperatur intervall          |     0 – 43 ° C (drift)    |
|     Vibrationer                  |     MIL-STD-810-metod 514,7 *<br>Procedure I CAT 4, 20                  |
|     Skydd                      |     MIL-STD-810-metod 516,7 *<br>Procedur IV, logistik                 |
|     Höjden                   |     Drift: 10 000 fot<br>Ej fungerande: 40 000 fot          |

**Alla referenser är till MIL-STD-810G Change 1 (2014)*

## <a name="next-steps"></a>Nästa steg

- [Distribuera Azure Stack Edge](azure-stack-edge-placeholder.md)
