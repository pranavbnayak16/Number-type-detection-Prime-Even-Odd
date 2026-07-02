# Number-type-detection-Prime-Even-Odd

// Dataflow style module using only "nand gates".
module primelogic_df(connectPorts a);
assign a.P = ˜( ˜(a.N[1] & a.N[0] & ˜(a.N[3] & a.N[3]))
& ˜(˜(a.N[1] & a.N[1]) & a.N[0] & a.N[2])
& ˜(a.N[1] & ˜(a.N[2] & a.N[2]) & ˜(a.N[3] & a.N[3]))
& ˜(a.N[1] & a.N[0] & ˜(a.N[2] & a.N[2])) );
assign a.E = ˜(a.N[0] & a.N[0]);
assign a.O = a.N[0];
endmodule

// Gate level style module using only "nor gates".
module primelogic_gl(connectPorts b);
wire w1, w2, w3, w4, w5, w6, w7, w8, w9;
nor n1(w1, b.N[0], b.N[0]);
nor n2(w2, b.N[1], b.N[1]);
nor n3(w3, b.N[2], b.N[2]);
nor n4(w4, b.N[3], b.N[3]);
nor n5(w5, w2, b.N[3], w1);
nor n6(w6, b.N[1], w1, w3);
nor n7(w7, b.N[3], b.N[2], w2);
nor n8(w8, b.N[2], w2, w1);
nor n9(w9, w5, w6, w7, w8);
nor n10(b.P, w9, w9);
nor n11(b.E, b.N[0], b.N[0]);
assign b.O = b.N[0];
endmodule


// Behavioral style module using "if-else" and "for loop" statements.
module primelogic_bh(connectPorts c);
always@*
begin
c.P = 1; c.E = 0; c.O = 0;
if(c.N % 2 == 0)
c.E = 1;
else
c.O = 1;
if(c.N == 0 || c.N == 1)
c.P = 0;
else
begin
for(int i = 2; i <= c.N / 2; i = i + 1)
begin
if(c.N % i == 0)
c.P = 0;
end
end
end
endmodule


// Interface.
interface connectPorts();
logic [3:0] N;
logic P, E, O;
endinterface


// Package including "Class"es to implement the design.
package primelogic_pkg;
class base;
virtual function check(input logic [3:0] N);
endfunction
endclass
class checkPrime extends base;
virtual function check(input logic [3:0] N);
return (N[1] & N[0] & ˜N[3]) | (N[2] & ˜N[1] & N[0]) |
(˜N[3] & ˜N[2] & N[1]) | (N[1] & N[0] & ˜N[2]);
endfunction
endclass
class checkEven extends base;
virtual function check(input logic [3:0] N);
return ˜N[0];
endfunction
endclass
class checkOdd extends base;
virtual function check(input [3:0] N);
return N[0];
endfunction
endclass
endpackage


// Testbench for primelogic_df
‘timescale 1us/1us
module TB_df();
connectPorts tbPort();
logic Pp, Ep, Op;
import primelogic_pkg::*;
base ob;
checkPrime obp;
checkEven obe;
checkOdd obo;
primelogic_df uut(.a(tbPort));
initial
begin
obp = new();
obe = new();
obo = new();
tbPort.N = 0;
#10 tbPort.N = 1;
#10 tbPort.N = 2;
#10 tbPort.N = 3;
#10 tbPort.N = 4;
#10 tbPort.N = 5;
#10 tbPort.N = 6;
#10 tbPort.N = 7;
#10 tbPort.N = 8;
#10 tbPort.N = 9;
#10 tbPort.N = 10;
#10 tbPort.N = 11;
#10 tbPort.N = 12;
#10 tbPort.N = 13;
#10 tbPort.N = 14;
#10 tbPort.N = 15;
end
always@*
begin
Pp = obp.check(tbPort.N);
Ep = obe.check(tbPort.N);
Op = obo.check(tbPort.N);
#5 $monitor("Module Output: N=%b, P=%b, E=%b, O=%b",
tbPort.N, tbPort.P, tbPort.E, tbPort.O);
$monitor("Package Output: N=%b, P=%b, E=%b, O=%b",
tbPort.N, Pp, Ep, Op);
if({tbPort.P, tbPort.E, tbPort.O} == {Pp, Ep, Op})
$monitor("Module output is equal to Package output");
else
$monitor("Module output is not equal to Package output");
$monitor();
end
endmodule


// Testbench for primelogic_gl
‘timescale 1us/1us
module TB_gl();
connectPorts tbPort();
logic Pp, Ep, Op;
import primelogic_pkg::*;
base ob;
checkPrime obp;
checkEven obe;
checkOdd obo;
primelogic_gl uut(.b(tbPort));
initial
begin
obp = new();
obe = new();
obo = new();
for(int i = 0; i < 16; i = i + 1)
begin
tbPort.N = i;
Pp = obp.check(tbPort.N);
Ep = obe.check(tbPort.N);
Op = obo.check(tbPort.N);
#10 $display("Module Output: N=%b, P=%b, E=%b, O=%b",
tbPort.N, tbPort.P, tbPort.E, tbPort.O);
$display("Package Output: N=%b, P=%b, E=%b, O=%b",
tbPort.N, Pp, Ep, Op);
if({tbPort.P, tbPort.E, tbPort.O} == {Pp, Ep, Op})
$display("Module output is equal to Package output");
else
$display("Module output is not equal to Package
output");
$display();
end
end
endmodule


// Testbench for primelogic_bh
‘timescale 1us/1us
module TB_bh();
connectPorts tbPort();
logic Pp, Ep, Op;
import primelogic_pkg::*;
base ob;
checkPrime obp;
checkEven obe;
checkOdd obo;
int i = 0;
primelogic_bh uut(.c(tbPort));
task verifyOutput();
begin
tbPort.N = i;
Pp = obp.check(tbPort.N);
Ep = obe.check(tbPort.N);
Op = obo.check(tbPort.N);
#10 $display("Module Output: N=%b, P=%b, E=%b, O=%b",
tbPort.N, tbPort.P, tbPort.E, tbPort.O);
$display("Package Output: N=%b, P=%b, E=%b, O=%b", tbPort.N, Pp,
Ep, Op);
if({tbPort.P, tbPort.E, tbPort.O} == {Pp, Ep, Op})
$display("Module output is equal to Package output");
else
$display("Module output is not equal to Package output");
$display();
end
endtask
initial
begin
obp = new();
obe = new();
obo = new();
repeat(16)
begin
verifyOutput();
i = i + 1;
end
end
endmodule





