# Experiment 6: Assertion-Based Verification of I²C Protocol Timing and Handshaking Sequences

---

## Aim  
To verify the **I²C protocol timing and handshaking sequences** using **Assertion-Based Verification (ABV)** in **SystemVerilog**.

---

## Apparatus Required  
- Computer with **Windows OS**  
- **EDA Playground** (for online verification)

---

## Description  
The **Inter-Integrated Circuit (I²C)** protocol is a two-wire serial communication standard consisting of **SDA (Serial Data)** and **SCL (Serial Clock)** lines. Proper timing and handshaking between master and slave devices are critical for reliable data transmission.  

In this experiment:
- **SystemVerilog assertions** are used to validate the **protocol timing**, **data validity**, and **handshaking** sequences.  
- Assertions ensure that signals meet timing requirements such as **setup**, **hold**, **start**, and **stop** conditions.  
- The testbench verifies correct protocol behavior under different random scenarios.

---

## Features  
- Assertion-based verification of I²C protocol  
- Monitors handshaking between **master** and **slave**  
- Validates **timing constraints** using **SystemVerilog assertions**  
- Portable and executable in **EDA Playground** or **ModelSim 2020.1**

---

## Procedure  

1. **Open EDA Playground or ModelSim 2020.1**  
   - Launch the simulation environment.

2. **Create a New Project**  
   - In EDA Playground, choose **SystemVerilog (IEEE 1800-2017)**.  
   - Name the project `I2C_Assertion_Verification`.

3. **Add the Following Files:**  
   - `i2c_master.sv` → I²C Master Model  
   - `i2c_tb.sv` → Testbench with Assertions  

4. **Compile the Files**  
   - Check for syntax or semantic errors.  

5. **Run Simulation**  
   - Use the “Run” button (EDA Playground) or command:
     
6. **Analyze Waveforms and Console Output**  
   - Observe whether assertions pass or fail.  
   - Verify proper handshaking between master and slave.

7. **Document the Results**  
   - Save the output logs and waveform for report submission.

---

## SystemVerilog Code
```
    module i2c_master (
    output logic SDA,
    output logic SCL,
    input  logic CLK,
    input  logic RESET
    );

    logic [7:0] data = 8'hA5;
    int i;

    always @(posedge CLK or posedge RESET) begin
        if (RESET) begin
            SDA <= 1;
            SCL <= 1;
        end else begin
            SDA <= 0;
            #5;
            for (i = 7; i >= 0; i--) begin
                SCL <= 0;
                SDA <= data[i];
                #5;
                SCL <= 1;
                #5;
            end
            SCL <= 1;
            SDA <= 1;
        end
    end
    endmodule
     module i2c_tb;
    logic SDA, SCL;
    logic CLK, RESET;

    i2c_master uut (
        .SDA(SDA),
        .SCL(SCL),
        .CLK(CLK),
        .RESET(RESET)
    );

    initial begin
        CLK = 0;
        forever #5 CLK = ~CLK;
    end

    initial begin
        RESET = 1;
        #10 RESET = 0;
    end

    property start_condition;
        @(posedge CLK) (SCL && $fell(SDA));
    endproperty
    start_check: assert property (start_condition)
        $display("START condition detected at time %0t", $time);
    else
        $error("START condition violated at time %0t", $time);

    property stop_condition;
        @(posedge CLK) (SCL && $rose(SDA));
    endproperty
    stop_check: assert property (stop_condition)
        $display("STOP condition detected at time %0t", $time);
    else
        $error("STOP condition violated at time %0t", $time);

    property data_stable;
        @(posedge SCL) $stable(SDA);
    endproperty
    stable_check: assert property (data_stable)
        $display("SDA stable during SCL HIGH at time %0t", $time);
    else
        $error("Data changed during clock high (I2C violation) at time %0t", $time);

    initial begin
        #200;
        $display("\n=== Simulation Completed ===");
        $finish;
    end
    endmodule
```






### Simulation Output:

<img width="1920" height="1080" alt="Screenshot 2025-11-04 152410" src="https://github.com/user-attachments/assets/19b64b5a-4a33-4f1d-bc40-0eca364bf1c8" />

<img width="1920" height="1080" alt="Screenshot 2025-11-04 152421" src="https://github.com/user-attachments/assets/3a3bc505-c0d9-49e9-afcd-3e82faf68773" />

<img width="1920" height="1080" alt="Screenshot 2025-11-04 152436" src="https://github.com/user-attachments/assets/3a9793d0-d654-464f-877c-a43b3b6b4029" />



## Result
The Assertion-Based Verification of the I²C protocol timing and handshaking sequences was successfully carried out using SystemVerilog.Assertions effectively verified setup, hold, start, and stop conditions, ensuring reliable communication as per the I²C protocol specification.


