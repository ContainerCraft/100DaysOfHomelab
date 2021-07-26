# Sherpa | Hardware Reference Architecture
Sherpa, The 100 Days of Homelab Reference Architecture is intended to demonstrate
practical up-cycling of effective and low/moderate cost comodity hardware.

Goals:
  - Cloud Scale like Infrastructure Modeling
  - Upgradeable Hardware to support moderately ambitious workloads
  - Reproducible with modest budget and or access to second hand comodity pc(s)

Non-Goals:
  - Production Viable Architecture
  - Elimination of all Single Points of Failure

## Operating System (OS)
| Flavor            | Status                            |
|:------------------|-----------------------------------|
| [CentOS Stream 8] | Recommended                       |
| [Fedora 34+]      | Support in development            |
| [Ubuntu 20.04+]   | Desktop only, method undocumented |
    
[CentOS Stream 8]:https://www.centos.org/centos-stream/
[Fedora 34+]:https://ubuntu.com/download/desktop
[Ubuntu 20.04+]:https://getfedora.org/en/server/

## Kargo Bare Metal Hosts (Starting Configuration)
  - Processor: 4c 8T
  - Memory: 16GB DDR3
  - Storage: SSD 256GB
  - Network: 
    - 1 x 1Gb Ethernet
    - 1 x 10Gb Fiber

## 3 Node Cluster Resource Total
|         | Minimum | Current |  Maximum  |
|:--------|:--------|:--------|:----------|
| Node #  |        1|        3| 3 or more | 
| CPU     |  4c/8t  | 12c/24t | 12c/24t   | 
| Memory  |   16GB  | 48GB    | 192GB     |
| Storage |  256GB  | 768GB   | 30TB      |
    
\*Primary storage should be SSD or better    
\**Estimated minimum budget if bying all parts to start should be aprox $400 USD    

## Hardware List
  - Dell Optiplex 7050 SFF ([on ebay](https://www.ebay.com/sch/i.html?_from=R40&_trksid=p2380057.m570.l1313&_nkw=Dell+Optiplex+7050+SFF&_sacat=0))
    - i7-6700 3.4GHz 4c
    - 16GB DDR3 RAM 1600MHz
    - 256GB SSD
  - 10Gb Networking Gear
    - [Asus 10Gb PCIe X4 SFP+ Fiber Network Card XG-C100F](https://www.asus.com/Networking-IoT-Servers/Wired-Networking/All-series/XG-C100F/)    
    - [QNAP 8x 1Gb Ethernet & 4x 10Gb SFP+ Managed Switch](https://www.qnap.com/en-us/product/qsw-m408s)    
    - [10Gb SFP+ DAC Twinax Network Cable](https://www.amazon.com/gp/product/B00WHS3NCA)    

## Itemized Prices for current CCIO Sherpa cluster
| Item           | count |   ea   | total + shipping |
|:---------------|:-----:|-------:|-----------------:|
| Optiplex(es)   | 3     | 299.00 |         1,046.66 |
| Network Cards  | 3     | 105.03 |           315.09 |
| Network Switch | 1     | 240.89 |           240.89 |
| Power Strip    | 1     |  19.99 |            19.99 |
| Network Cable  | 3     |  05.03 |            15.09 |
|                |       |        |                  |
| GRAND TOTAL    |       |        |         1,637.72 |

--------------------------------------------
\* Prices & Links as of 07/24/2021    
\** CCIO and it's contributors are not sponsored by or affiliated with any of the hardware or vendors referenced    
