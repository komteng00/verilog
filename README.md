# help! slotmachine verilog

// Hi i tried to make slotmachine, but its not working so far. Everything work except (module slotmachine) can someone help me with this code?

module lfsr (clk, reset_b, seed_pulse, cnt, q);
  input wire clk, reset_b, seed_pulse;
  input wire [15:0] cnt;
  
  output wire [15:0] q;
  
  reg [15:0] r_reg;
  wire [15:0] r_next;
  wire feedback_value;
  
  always @(posedge clk, negedge reset_b)
    begin
      if (~reset_b)
        r_reg <= 16'b1;
      else if (seed_pulse)
        r_reg <= cnt;
      else 
        r_reg <= r_next;
    end
  
  assign feedback_value = r_reg[9] ^ r_reg[5] ^ r_reg[0];
  
  assign r_next = {feedback_value, r_reg[15:1]};
  assign q = r_reg;
endmodule

module counter (clk, reset_b, start, cnt, seed_pulse);
  input wire clk, reset_b, start;
  output reg [15:0] cnt;
  output reg seed_pulse;
  
  always @(posedge clk, negedge reset_b)
    begin
      if (~reset_b)
        cnt <= 16'b0;
      else
        cnt <= cnt + 1;
    end
  
  reg pushed;
  
  always @(posedge clk, negedge reset_b)
    begin
      if (~reset_b)
        pushed <= 1'b0;
      else if (~pushed & start)
        pushed <=1'b1;
      else if (~start)
        pushed <= 1'b0;
    end
  
  always @(posedge clk, negedge reset_b)
    begin
      if (~reset_b)
        seed_pulse <= 1'b0;
      else if (~pushed & start)
        seed_pulse <= 1'b1;
      else
        seed_pulse <= 1'b0;
    end
endmodule

//random1

module random1 (clk, reset_b, start, q, random1);
  input wire clk, reset_b, start;
  input wire [15:0] q;
  output reg [15:0] random1;
  
  parameter magic = 16'd7;
  
  reg [15:0] cnt;
  reg randomed;
  always @(posedge clk, negedge reset_b)
    begin
      if (~reset_b)
        begin
          cnt <= 16'b0;
          random1 <= 16'b0;
          randomed <= 1'b0;
        end
      else if (~start)
        begin
          cnt <= 16'b0;
          random1 <= 16'b0;
          randomed <= 1'b0; //random 값을 0으로
        end
      else if (start & (cnt == magic) & ~randomed)
        begin
          random1 <= q%10;
          cnt <= cnt;
          randomed <= 1'b1; 
        end
      else
        begin 
          random1 <= random1;
          cnt <= cnt + 1;
          randomed <= randomed;
        end
    end
  
endmodule

//random2

module random2 (clk, reset_b, start, q, random2);
  input wire clk, reset_b, start;
  input wire [15:0] q;
  output reg [15:0] random2;
  
  parameter magic = 16'd3;
  
  reg [15:0] cnt;
  reg randomed;
  always @(posedge clk, negedge reset_b)
    begin
      if (~reset_b)
        begin
          cnt <= 16'b0;
          random2 <= 16'b0;
          randomed <= 1'b0;
        end
      else if (~start)
        begin
          cnt <= 16'b0;
          random2 <= 16'b0;
          randomed <= 1'b0; 
        end
      else if (start & (cnt == magic) & ~randomed)
        begin
          random2 <= q%10;
          cnt <= cnt;
          randomed <= 1'b1; 
        end
      else
        begin 
          random2 <= random2;
          cnt <= cnt + 1;
          randomed <= randomed;
        end
    end
  
endmodule

//random3

module random3 (clk, reset_b, start, q, random3);
  input wire clk, reset_b, start;
  input wire [15:0] q;
  output reg [15:0] random3;
  
  parameter magic = 16'd9;
  
  reg [15:0] cnt;
  reg randomed;
  always @(posedge clk, negedge reset_b)
    begin
      if (~reset_b)
        begin
          cnt <= 16'b0;
          random3 <= 16'b0;
          randomed <= 1'b0;
        end
      else if (~start)
        begin
          cnt <= 16'b0;
          random3 <= 16'b0;
          randomed <= 1'b0;
        end
      else if (start & (cnt == magic) & ~randomed)
        begin
          random3 <= q%10;
          cnt <= cnt;
          randomed <= 1'b1; 
        end
      else
        begin 
          random3 <= random3;
          cnt <= cnt + 1;
          randomed <= randomed;
        end
    end
  
endmodule


module slotmachine (in_money, in_value1, in_value2, in_value3, random1, random2, random3, out_money);
  input wire [6:0] in_money;
  input wire [3:0] in_value1, in_value2, in_value3;
  output reg [15:0] random1, random2, random3;
  output reg [9:0] out_money;
       
     wire in_value1 <= 4'b8;
     wire in_value2 <= 4'b3;
     wire in_value3 <= 4'b5;
     wire in_money <= 7'b50;
      
  always @(posedge clk, negedge reset_b)
    begin
      if ((in_value1 != random1) & (in_value2 != random2) & (in_value3 != random3))
          out_money <= in_money;
      
      else if ((in_value1 == random1) & (in_value2 != random2) & (in_value3 != random3))
       	out_money == 2*in_money;   
      
      else if((in_value1 != random1) & (in_value2 == random2) & (in_value3 != random3))
         out_money == 2*in_money;
      
      else if((in_value1 != random1) & (in_value2 != random2) & (in_value3 == random3))
         out_money == 2*in_money;
      
      else if((in_value1 == random1) & (in_value2 == random2) & (in_value3 != random3))
         out_money == 5*in_money;
      
      else if((in_value1 == random1) & (in_value2 != random2) & (in_value3 == random3))
         out_money == 5*in_money;
      
      else if((in_value1 != random1) & (in_value2 == random2) & (in_value3 == random3))
         out_money == 5*in_money;
      
      else if((in_value1 == random1) & (in_value2 == random2) & (in_value3 == random3))
         out_money == 10*in_money;
    end
  
endmodule


module tb;
  
  reg clk, reset_b, start;
  
  wire [15:0] seed, q, random1, random2, random3;
  wire [3:0] in_value1, in_value2, in_value3;
  wire [6:0] in_money;
  wire [9:0] out_money;
  wire 	      seed_pulse;
  
  counter u1 (.clk(clk), .reset_b(reset_b), .start(start), .cnt(seed), .seed_pulse(seed_pulse));
  lfsr u2 (.clk(clk), .reset_b(reset_b), .seed_pulse(seed_pulse), .cnt(seed), .q(q));
  random1 u3 (.clk(clk), .reset_b(reset_b), .start(start), .q(q), .random1(random1));
  random2 u4 (.clk(clk), .reset_b(reset_b), .start(start), .q(q), .random2(random2));
  random3 u5 (.clk(clk), .reset_b(reset_b), .start(start), .q(q), .random3(random3));
  slotmachine u6 (.in_money(in_money), .in_value1(in_value1), .in_value2(in_value2), .in_value3(in_value3), .out_money(out_money));
  
  initial
    clk=0;
  always
    #5 clk = ~clk;
  
  initial
    begin
      reset_b = 0;
      #8 reset_b = 1;
    end
  
  initial
    begin
      	start = 0;
      #38 start = 1;
      #100 start = 0;
      #50 $finish;
    end
  
  initial
    $monitor("time: %3d, start: %b, seed: %d, lfsr: %d, random1: %d, random2: %d, random3: %d, in_money: %d, in_value: %d, %d, %d, out_money: %d", $time, start, seed, q, random1, random2, random3, in_money, in_value1, in_value2, in_value3, out_money);
             
  initial
  begin
    $dumpfile("dump.vcd"); 
    $dumpvars;
  end
endmodule
