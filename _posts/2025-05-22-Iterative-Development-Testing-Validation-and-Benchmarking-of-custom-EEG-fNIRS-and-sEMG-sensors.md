---
title:  "Iterative Development, Testing, Validation and Benchmarking of custom EEG fNIRS and sEMG sensors"
mathjax: true
layout: post
categories: media
---

## Abstract

This paper presents the iterative development, testing, validation, and benchmarking of custom biosignal acquisition systems designed for neurophysiological monitoring, including functional near infrared spectroscopy (fNIRS), electroencephalography (EEG), and surface electromyography (sEMG). The fNIRS subsystem employs dual-wavelength light-emitting diodes (LEDs) and a dedicated Python simulation framework to model light absorption and optimize sensor placement, while the vascular occlusion test is explained in detail to assess its capability to show tissue oxygenation dynamics. In parallel, the custom EEG board leverages a high speed Teensy 4.1 microcontroller in conjunction with ADS1299 analog to digital converters, and integrates an (organic light-emitting diode) OLED FeatherWing display via a Feather adapter to create an intuitive user interface, as well as an ESP32 Feather module for wireless data streaming. Additionally, a custom sEMG board based on an STM32H7 microcontroller is developed to facilitate real-time movement classification and support neurorehabilitation applications. Benchmarking of the custom devices against commercial alternatives, including the OpenBCI Cyton, MiniSim EEG signal simulator, and MyoWare sensor, demonstrates their reliability, accuracy, and overall potential for practical applications in brain–computer interface research and neurorehabilitation.

## Keywords

fNIRS, EEG, sEMG, biomedical signal acquisition, neurorehabilitation, augmented reality, BCI

## Introduction

In recent years, the need for accessible and affordable biomedical devices has grown significantly, both for public health applications and research purposes. Cost-effective, modular systems enable a broader range of users to monitor and understand complex physiological signals while driving innovation in neuroscience and rehabilitation [4], [5], [6]. This paper presents an integrated approach to the iterative development, testing, validation, and benchmarking of custom functional near infrared spectroscopy (fNIRS), electroencephalography (EEG), and surface electromyography (sEMG) sensors, demonstrating that high-performance biomedical instrumentation can be achieved without compromising affordability. The development of those sensors will lead to their incorporation into the framework of the NeuroSuitUp platform and NeuroSuitUp/HEROES projects [1],[2].

## Functional Near Infrared Spectroscopy (fNIRS)

In the logic of prototyping iteratively, and allowing for the parallel development and testing of different photodiodes for the fNIRS biomedical sensor [3], the decision to split the light-emitting diode (LED) grid emitting the light at the wavelength of 730nm and 850nm into the brain for the detection of oxygenated and deoxygenated hemoglobin accordingly, from the photodiode module meant to act as a detector of the light as it passes human tissue was taken. In LEDs, radiant intensity refers to the amount of optical power, sometimes called radiant flux, which is emitted by the LED per unit of solid angle in a specific direction. Radiant intensity is a measure of how much light the LED emits in a particular direction, making it a directional quantity, which is particularly important when designing optical systems, as it helps determine how much light will be available at a given distance and angle from the emitter LED. Most fNIRS systems take advantage of the dual wavelength LEDs by Marubeni, which have the ability to produce either 750nm or 850nm light according to which cathode of the LED is given power. The Marubeni LEDs can have a current of up to 500mA at 750nm and up to 1A at 850nm, and radiant intensity up to 230 mW/sr and 360 mW/sr for the 750nm and 850nm wavelengths. In an attempt to make an informed decision on the radiant intensity of the LEDs on this development iteration for the fNIRS sensor, a python script was written meant to simulate the absorption of emitted light by the brain using the corresponding absorption coefficients from the bibliography. This python script is used to validate the minimum radiant intensity needed for absorption of emitted light from the biological tissues comprising the human head at different wavelengths and reject LEDs which are not capable of emitting light with the necessary radiant intensity to be absorbed by the brain before scattering. In order to be able to choose accessible and affordable electronic components, two different LEDs were selected in order to create a channel of fNIRS information, meaning an emission of light at 730nm and 850nm, leading to a a voxel of fNIRS information. In order to give an accurate measure of loss to the available spatial resolution with the usage of these LEDs, the Marubeni LEDs are 2.7mm in width and 3.5mm in height, while the custom fNIRS channel are about 3mm width and height each, so in this way the possible spatial resolution is half compared to using the Marubeni LEDs, however their affordability and accessibility in procuring them makes these LEDs an appropriate choice for research purposes.

The Python code calculates the light penetration depth in human tissue for two LEDs with different wavelengths of 730 nm and 850 nm. It simulates how light from these LEDs penetrates through layers of the human head taking into account the absorption coefficients of the scalp, skull, and brain based on their absorption coefficients and the Beer-Lambert Law, which describes how light is absorbed as it passes through a medium. Here, it should be noted that with the absorption coefficients for the scalp, skull and brain defined from the according bibliography, along with the wavelength of emission of the LEDs and their radiant intensity, a python script meant to simulate the maximum depth of absorption of light by the human head was created. The code then iterates over the two LEDs and calculates the penetration depths for each LED, computing the total penetration depth and brain penetration depth and printing the results for each LED. The code assumes the initial power of the light is equal to the radiant intensity of the LED, then the calculation of the depth at which only 1% of the light remains after passing through the scalp is performed using the formula:

`(1) scalp depth = -ln(0.01)/ scalp_absorption`

This is derived from the Beer-Lambert law, which states that light intensity decreases exponentially with depth. Similarly, the code calculates the depth at which only 1% of the light remains after passing through the skull:

`(2) skull depth = -ln(0.01)/ skull_absorption`

After calculating the depths for the scalp and skull, it computes the remaining power of the light after passing through these layers using the exponential decay formula:

`(3) power_after_scalp = initial_power * e^(-scalp_absorption * scalp_depth)`

`(4) power_after_skull = power_after_scalp * e^(-skull_absorption * skull_depth)`

Finally, it calculates the depth into the brain where only 1% of the remaining light, after passing through the scalp and skull is left:

`(5) brain_depth = -ln(0.01 / power_after_skull) / brain_absorption`

The total penetration depth is the sum of the depths through the scalp, skull, and brain is calculated then.

`(6) total_depth = scalp_depth + skull_depth + brain_depth`

For comparison, the same code when executed for the Marubeni LEDs, provides the depth into the brain of 33.32 mm at the 730nm wavelength and 51.24 mm at the 850nm wavelength. In order to drive the LEDs, a constant current driver was needed so the The TP892630-Q1 Three-Channel Linear LED Driver from Texas Instruments was chosen to prototype a low cost board with a few channels for validation and functional testing. This LED driver was selected due to its availability, affordability, and ability to support a round-robin sequencing scheme, which is crucial for minimizing crosstalk between the LEDs. Crosstalk can occur when multiple LEDs emit light simultaneously, causing interference in the detected signals. To mitigate this, the LEDs are driven in a sequential manner, ensuring that only one LED is active at a given time while the others remain off. This controlled timing approach enhances the accuracy of the recorded signals by preventing overlapping light sources from contaminating the readings. The TP892630-Q1's capability to regulate current across multiple channels further ensures stable and consistent illumination, making it well-suited for fNIRS applications requiring precise light modulation.

### fNIRS Sensor Testing

The fNIRS testing procedure, commonly referred to as the Vascular Occlusion Test (VOT), is a robust method for assessing tissue oxygenation and vascular function. In this procedure, an fNIRS sensor is placed on a target muscle to continuously measure tissue oxygen saturation (StO2). The test is divided into three distinct phases. Initially, the sensor records stable StO2 levels over a three-minute period, establishing a reference point for subsequent measurements. A sphygmomanometer cuff is applied proximally to the sensor and inflated rapidly to approximately 50 mmHg above the subject's systolic blood pressure. This inflation occludes both arterial inflow and venous outflow, inducing a state of ischemia. During this three-minute occlusion, the fNIRS device captures the gradual decrease in StO2 as the tissue consumes its available oxygen, resulting in a desaturation curve. After the occlusion period, the cuff is quickly deflated, allowing blood to flow back into the tissue. The sensor then records the hyperemic response, the rapid increase in StO2 as the tissue reoxygenates. This phase continues until the oxygen saturation returns to near baseline levels. The data collected during these phases are used to extract key parameters such as baseline StO2, the desaturation slope or the rate of oxygen decline, minimum StO2 achieved during occlusion, the reperfusion slope or rate of oxygen recovery, and the rise time or time taken to reach maximum StO2 post-deflation. Together, these metrics provide a detailed picture of the microvascular function and the dynamic changes in tissue oxygenation during vascular stress and recovery.

**Table 1: Absorption coefficients at different wavelengths**

| Wavelength (nm) | Scalp (1/mm) | Skull (1/mm) | Brain (1/mm) |
|-----------------|--------------|--------------|-------------|
| 730             | 0.017        | 0.033        | 0.025       |
| 850             | 0.018        | 0.040        | 0.025       |

**Table 2: Results for each wavelength and LED**

| LED          | Wavelength (nm) | Radiant Intensity (mW/sr) | Total Penetration Depth (mm) | Depth into Brain (mm) |
|--------------|-----------------|---------------------------|------------------------------|----------------------|
| Creel.ED FR  | 730             | 135                       | 422                          | 12                   |
| SFH4350 LED  | 850             | 200                       | 398                          | 27                   |

### INIRS Photocmitters and Photodetectors Locations

The INIRS device is designed for motor imagery recordings, specifically targeting brain activity related to left and right-hand movements. To ensure optimal sensor placement, the INIRS Optodes' Location Decider (fOLD) toolbox in MATLAB was utilized [8], providing guidance on the brain regions that need to be covered based on a prior motor imagery experiment. The key anatomical regions selected for the experiment, with a specificity threshold of 30%, include the left and right Precentral and Postcentral areas, as well as the Frontal Sup 2 and Frontal Mid 2 regions on both hemispheres. The toolbox generated a 1020 system-based map indicating the optimal positions for INIRS sources and detectors, ensuring precise coverage of the motor cortex for capturing cortical activation during imagined movement tasks.

## Electroencephalography (EEG)

The EEG board is based on a previous thesis [7], with this iteration standing to improve on the design of the electronics along with working on a more robust testing and validation of its biomedical function procedure. In order to upgrade the microprocessor from the previous design and also raise the sampling frequency to an appropriate level consistent with other EEG data acquisition boards, the Teensy 4.1 microprocessor was chosen, which is capable of reaching up to 500 Mhz sampling frequency allowing for more data heavy EEG recordings. The same design as before is being kept in regards with the ADS1299 which are Analog to Digital Converters specially designed and produced by Texas Instruments for EEG instrumentation. The ADS1299 four channel integrated circuits are used for data acquisition, interfacing directly with the Teensy 4.1, which processes EEG signals with high precision. To enhance usability, the Teensy 4.1 can be connected to an organic LED (OLED) FeatherWing display and integrated into a Feather adapter, leveraging Adafruit's electronics ecosystem. This setup allows for a compact and modular design, making it easy to create an interactive user interface for monitoring EEG data in real time. Additionally, a secondary ESP32 Feather board is responsible for establishing wireless communication, enabling seamless data transmission via the lab streaming layer (LSL) [9]. By combining these elements, the system provides an intuitive and flexible EEG acquisition platform that benefits from Adafruit's well supported hardware ecosystem.

It should be noted that the open-source Mark III task from openBCI [10] is being used to support the dry electrodes and allow for a more streamlined and comfortable for the users' procedure, for the EEG recordings. Another integral part for the identification of movement noise to be flagged during the data acquisition, in order to be dealt with in the EEG preprocessing is the BNO085 inertial measurement unit, which along with its capabilities in identifying movement noise, could also be useful in the future when it comes to developing Augmented Reality applications which require head tracking along with sampling of electroencephalography signals.

## Surface Electromyography (sEMG) board

The sEMG board is based on a previous thesis as well [11] and it is meant to collect data for the future application of neurorehabilitation. At the current time there have been previous and ongoing dissertations which focus on the development of machine learning classification models based on sEMG signals which are meant to identify movement in real time as the recording is taking place. For the hardware to be able to support such capabilities the STM32H7 microcontroller was chosen mostly for its architecture which consists of an M7 Cortex processor which is capable of running machine learning models on the edge, while the M4 cortex of the STM32H7 handles the necessary data recording procedure. The coding of the STM32H7 microprocessor using the STM32 CubeIDE while keeping most of the electronic circuit intact and ensuring a robust testing of the device proves to be the main challenge of developing this particular board. However, With the M7 and M4 two coprocessors working together future neurorehabilitation and augmented reality applications could be developed, which could be especially benefited and customized to fit the capabilities of the sEMG sensor.

## Benchmarking

Benchmarking the custom EEG and sEMG devices involves a rigorous evaluation of their performance against commercially available alternatives to ensure reliability, accuracy, and overall functionality. For the EEG board, the OpenBCI Cyton will serve as a reference device, allowing for direct comparison of signal quality, noise levels, and sampling rates. Additionally, the MiniSim EEG signal simulator will be used to generate standardized EEG waveforms, enabling precise validation of the custom device's signal acquisition capabilities under controlled conditions. Similarly, the custom sEMG board will be benchmarked against the MyoWare sensor, assessing factors such as signal fidelity and response time. The benchmarking process will involve robust testing procedures, including controlled data acquisition sessions, statistical analysis of signal integrity, and real-time performance evaluations. By systematically comparing the recorded signals from the custom devices with those obtained from commercial counterparts, the study will highlight the advantages and limitations of the proposed sensors. This approach will ensure that the developed hardware meets the necessary standards for practical applications, including neurorehabilitation and brain-computer interface research.

**Figure 1:** Hardware overview for EEG board supporting wireless LSL streaming

**Figure 2:** sEMG Hardware Overview for Data Acquisition and real time classification of muscle movement.

## Expected Results & Discussion

Validation through standardized tests and compliance to standardization allow for testing procedures with the minimal amount of risk involved. These protocols and standards provide a framework for evaluating sensor accuracy, safety, and performance while addressing possible hazards associated with electrical, mechanical, and optical components. Moreover, to further reduce biases and mitigate any emerging risks, the Aristotle university's ethics committee rigorously reviews and approves each testing procedure for the sensors, and several medical professionals are consulted through the experiment design procedure. This allows for evaluations of participant safety, data integrity, and the use of proper control mechanisms, fostering transparency and accountability in every stage of sensor development and testing. When it comes to iteratively developing biomedical sensors and electronics in general, challenges emerge with ensuring basic functionality being the first step in the iterative design process. The next phases of iterative development are focused on the appropriate sensitivity and capability of acquiring the necessary biomedical signals and filtering them accordingly. Ideally, through continuous testing and data analysis of the acquired signals supervised by medical and academic professionals who are familiar with the procedure, the biomedical capabilities of the system are proved, and then the electronics validation process through rigorous simulations in design programs along with possible experiments to see how the sensors react when faced with electromagnetic interference. All these iterations are helpful in achieving the validation of the custom biomedical sensors in EEG, EMG and fNIRS.

## References

[1] K. Mitsopoulos, et al. Neurosutup: System architecture and validation of a motor rehabilitation wearable robotics and serious game platform. Sensors, 23, 3 2023.

[2] Athanasiou A, et al. Neurorehabilitation Through Synergistic Man-Machine Interfaces Promoting Dormant Neuroplasticity in Spinal Cord Injury: Protocol for a Nonrandomized Controlled Trial JMIR Res Protoe 2022;11(9):e41152 doi: 10.2196/41152

[3] O. Chondroudis, "Design and Implementation of a Device for Recording Brain Activity based on functional Near-Infrared Spectroscopy (fNIRS)," M.Sc. thesis, Dept. of Information and Electronic Engineering, Int. Hellenic Univ., Greece, Jun. 22, 2024. [Online].

[4] O'Brien WJ, et al., "minjMIRS: an open hardware solution for wearable whole-head high-density functional near-infrared spectroscopy," Biomed. Opt. Express 15, 5625-5644 (2024)

[5] OpenBCI: Open Source Brain-Computer Interfaces. Retrieved March 30, 2025, from https://openbei.com/

[6] OpenEMG Arduino Sensor, Retrieved March 30, 2025, from https://charleslabs.fr/en/project-OpenEMG+Arduino+Sensor

[7] Vasilopoulos VG, Development of motor imagery brain-computer interface for use in stroke patient rehabilitation, M.Sc. thesis, Dept. of Biomedical Engin., Aristotle Univ. of Thessaloniki, Greece, 2023.

[8] G. A. Zimeo Morais, J. B. Balardin, and J. R. Sato, 'fNIRS Optodes' Location Decider (fOLD): A Toolbox for Probe Arrangement Guided by Brain Regions-of-Interest', Sci. Rep., vol. 8, no. 3341, 2018

[9] LSL, "Lab Streaming Layer (LSL) protocol," Available: https://labstreaminglayer.org/. Accessed: Mar. 30, 2025.

[10] OpenBCI, 'Ultracortex'. Accessed: Mar. 26, 2025. Available: https://github.com/OpenBCI/Ultracortex.

[11] V. Mantoiu, "Design, development and validation of an agonist-antagonist pair sEMG flexible PCB," M.Sc. thesis, School of Med., Aristotle Univ. of Thessaloniki, Greece, Jan. 2024.

## Acknowledgements

This research project was supported by the Hellenic Foundation for Research and Innovation (H.F.R.I.) https://www.eildek.gr under the "2nd Call for H.F.R.I. Research Projects to support Faculty Members & Researchers" (Project Number: 4391).

**Figure 3:** Mark III open-source head set for EEG dry electrodes and fNIRS optodes. The fNIRS locations for motor function imaging are marked with orange color.
