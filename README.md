# galois-lfsr
This is a fork of https://opencores.org/project/galois_lfsr.

This project implements the Galois Linear-feedback Shift Register (LFSR) in VHDL, and can be used for applications such as PRBS generation & synchronisation, CRC computations, scrambling & descrambling, cryptography, etc. This design is very generic / parameterisable, in the sense that it is intelligent enough to be able to "create" (or generate) the LFSR structure based on user input (a VHDL generic). In the lfsr entity (galois-lfsr.vhdl), there is a generic named taps, which allows you to input a vector of tap locations for the LFSR.

For example, to create an LFSR with a polynomial of x^8 + x^2 + x + 1, all you have to do is specify a tap vector of (0,1,2,8), i.e. register outputs 0, 1, 2, and 8 are tapped. After specifying the tap vector with the correct tap locations, simply map the vector to the LFSR instance, as follows:

	<code>
	/* user.vhdl example file. */
	i_lfsr: entity work.lfsr(rtl) generic map(
		taps => (
			/* Specify the tap vector here. */
			0|1|2|8=>true,
			7 downto 3=>false
		)
	)
	port map(...);
	</code>

Note that the design assumes the largest tap location is fed back to all the previous taps, by means of connecting to the inputs of each XOR gate of previous taps.

To simulate the design with Mentor Graphics Questa/ModelSim, simply cd into the testbench/questa folder, and execute simulate.sh from the Unix prompt:
$ ./simulate.sh

If you have ModelSim/QuestaSim installed, the GUI will appear immediately after you run the script.

Currently, we provide only the simulation script for Linux/Unix. Contact us if you need help with simulating this project on Windows, and we will send you separate instructions.

If you are using any other simulator, do let us know how this core works with your tool. One of the goals of this project is to make this core as vendor independent as possible.

This design synthesises in Quartus. In the coming weeks, we will be verifying this core on hardware. We also have plans to verify on Xilinx FPGAs. Stay tuned for updates.

Note that although you set the VHDL-2008 option in Quartus, it doesn't yet support boolean_vector and integer_vector. So we need to add these definitions for synthesis. You can find them in a separate file (packages/pkg-types.vhdl):

	<code>
	type boolean_vector is array(natural range <>) of boolean;
	type integer_vector is array(natural range <>) of integer;
	</code>

These VHDL-2008 additions are very useful, so request your tool vendor for this support, if they haven't already.
