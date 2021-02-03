modulestate_machine_moore(clk, reset, in, out);
parameter zero=0, one1=1, two1s=2;
output out; inputclk, reset, in;
reg out; reg[1:0] state,next_state;
// Implement the state register 
always@(posedgeclkorposedge reset)begin
if(reset)
    state <= zero;
else
    state <=next_state;
end

always@(state or in)begin
case(state)
   zero:begin//last input was a zero out = 0;
if(in)
next_state=one1;
else
next_state=zero;
end
   one1:begin//we've seen one 1 out = 0;
if(in)
next_state=two1s;
else
next_state=zero;
end
   two1s:begin//we've seen at least 2 ones out = 1;
if(in)
next_state=two1s;
else 
next_state=zero;
end
default://in case we reach a bad state out = 0;
next_state=zero;
endcase
end
// output logic 
always@(state)begin
case(state)
    zero: out <=0;
     one1: out <=0;
     two1s: out <=1;
default: out <=0;
endcase
end
endmodule
Test Bench
timescale1ns/1ps
`include"state_machine_moore.v"
modulestate_machine_moore_tb;
regclk, reset, in;
wire out;

// instantiate state machine 
state_machine_moore DUT (clk, reset, in, out);
initial
forever#5clk=~clk;

initialbegin
    reset =1'b1;
clk=1'b0;
    in =0;
#6;
    reset =1'b0;
    for(integeri=0;i<10;i=i+1)begin
@(negedgeclk); #1;
       in =$random;
if(out ==1'b1)
$display("PASS : Sequence 11 detected \n");
        end
        #50;
       $finish;
end
endmoduleMealy state Machine

modulestate_machine_mealy(clk, reset, in, out);
inputclk, reset, in;
output out;
reg out, state,next_state;
parameter zero=0, one=1;
//Implement the state register 
always@(posedgeclk, posedge reset) begin
if(reset)
    state <= zero;
else
     state <=next_state;
End
always@(in or state)
case(state)
  zero:begin
// last input was a zero
     out=0;
if(in)
next_state= one;
else
next_state= zero;
 end
  one:begin//seen one
if(in)begin
next_state= one;
    out=1;
endelsebegin
next_state= zero;
    out=0;
end
end
endcase
endmodule



Test bench


`timescale1ns/1ps
`include"state_machine_mealy.v"
modulestate_machine_mealy_tb;
regclk, reset, in;
wire out;

// instantiate state machine 
state_machine_mealy DUT (clk, reset, in, out);
initial
forever#5clk=~clk;

initialbegin
    reset =1'b1;
clk=1'b0;
    in =0;
#6;
    reset =1'b0;
    for(integeri=0;i<10;i=i+1)begin
@(negedgeclk); #1;
      in =$random;
if(out ==1'b1)
$display("PASS : Sequence 11 detected i=%d\n“, i);
       end
       #50;
       $finish;
end
