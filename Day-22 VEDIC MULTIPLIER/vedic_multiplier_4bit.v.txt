`timescale 1ns / 1ps

module vedic_multiplier_4bit(
    input [3:0]a,
    input [3:0]b,
    output [8:0]product
    );
    wire [15:0]w;
    wire [7:0]s;
    wire [2:0]c;
    wire [7:0]p;
    wire [3:0]rc2;
    wire [3:0]rc3;
    
    assign p[1:0]= w[1:0];
    assign rc2[1:0]=w[3:2];
    
    vedic_multiplier_2bit v1(a[1:0],b[1:0],w[3:0]);    
    vedic_multiplier_2bit v2(a[1:0],b[3:2],w[7:4]);
    vedic_multiplier_2bit v3(a[3:2],b[1:0],w[11:8]);
    vedic_multiplier_2bit v4(a[3:2],b[3:2],w[15:12]);
    
    ripple_carry_adder rca1(s[3:0],c[0],w[7:4],w[11:8],1'b0);
    
    assign rc2[3:2]=2'b00;
    
    ripple_carry_adder rca2(s[7:4],c[1],s[3:0],rc2[3:0],1'b0);
    
    or(rc3[3],c[0],c[1]);
    assign p[3:2]=s[5:4];
    assign rc3[1:0]=s[7:6];
    assign rc3[2]=1'b0;
    
    ripple_carry_adder rca3(p[7:4],c[2],w[15:12],rc3[3:0],1'b0);
    
    assign product={c[2],p[7:0]};
    
endmodule


module vedic_multiplier_2bit(
    input [1:0] a,
    input [1:0] b,
    output [3:0]product   
    );
    wire [3:0]p;
    wire [3:0]w;
    
    and(p[0],a[0],b[0]);
    and(w[0],a[0],b[1]);
    and(w[1],a[1],b[0]);
    and(w[2],a[1],b[1]);
       
    half_adder adder_1(w[0],w[1],p[1],w[3]);
    half_adder adder_2(w[3],w[2],p[2],p[3]);
    
    assign product={p[3:0]};
    
endmodule

module half_adder(
    input a,b,
    output sum,carry );
    
    assign sum = a ^ b ;
    assign carry = a & b ;
endmodule

module ripple_carry_adder(
    output [3:0] sum,
    output cout,
    input [3:0] a,
    input [3:0] b,
    input cin
);
    wire [3:0] c;
    fa adder1(sum[0], c[0], a[0], b[0], cin);
    fa adder2(sum[1], c[1], a[1], b[1], c[0]);
    fa adder3(sum[2], c[2], a[2], b[2], c[1]);
    fa adder4(sum[3], c[3], a[3], b[3], c[2]);
    assign cout = c[3];
endmodule

module fa(
    output sum, carry,
    input a, b, cin
);
    assign sum = a ^ b ^ cin;
    assign carry = (a & b) | (b & cin) | (cin & a);
endmodule