# Mod-14-Up_Down-counter
UVM-based verification of a 4-bit up/down counter with synchronous reset and load. Randomized stimulus, a reference model, and a scoreboard ensure correctness. Handles edge cases like reset mid-operation and wrap-around. Demonstrates RTL design and UVM verification
This project implements a Universal Verification Methodology (UVM) testbench to verify a 4-bit up/down counter RTL design. Below is a detailed breakdown of how each component works together to validate the counter's functionality.

1. DUT (Design Under Test) - up_down_counter
Functionality:

Counts up (up_down=1) or down (up_down=0).

Resets to 0 when reset=1.

Loads a value (data_in) when load=1.

Wraps around at 0 and 13.

2. UVM Testbench Architecture
A. Top-Level (top.sv)
Instantiates the DUT and interface (counter_if).

Generates the clock.

Sets the virtual interface in uvm_config_db for UVM components.

Starts the UVM test using run_test().

B. Test (test.sv)
Configures the testbench:

Sets agent modes (UVM_ACTIVE for source, UVM_PASSIVE for destination).

Configures the number of agents.

Passes the virtual interface to all components.

Executes the test:

Runs the virtual sequence (v_sequence) 10 times.

Uses objections to control simulation duration.

C. Environment (env.sv)
Instantiates:

Source Agent Top (src_agt_top) → Drives stimulus.

Destination Agent Top (dest_agt_top) → Monitors outputs.

Scoreboard (sb_h) → Checks correctness.

Reference Model (ref_h) → Predicts expected behavior.

Virtual Sequencer (v_seqr_h) → Coordinates sequences.

Connections:

Source Monitor → Reference Model → Sends input transactions.

Reference Model → Scoreboard → Sends expected outputs.

Destination Monitor → Scoreboard → Sends DUT outputs.

3. Agents & Sub-Components
A. Source Agent (src_agent.sv) (Active)
Driver (src_drv.sv) → Sends randomized inputs (reset, load, up_down, data_in) to DUT.

Monitor (src_mon.sv) → Captures DUT inputs and sends them to the reference model.

Sequencer (src_sequencer.sv) → Manages sequence execution.

B. Destination Agent (dest_agent.sv) (Passive)
Monitor (dest_mon.sv) → Observes DUT output (count) and sends it to the scoreboard.

(No driver since it's passive).

4. Sequences & Stimulus Generation
A. Virtual Sequence (v_sequence.sv)
Coordinates between source and destination sequencers.

Starts the main sequence on the source sequencer.

B. Main Sequence (main_sequence.sv)
Generates randomized transactions (write_xtn) with constraints:

reset (30% probability).

load (25% probability).

up_down (50% probability).

data_in (0 to 13).

5. Reference Model & Scoreboard
A. Reference Model (ref_model.sv)
Predicts the expected counter behavior based on inputs.

Updates the transaction with the expected count value.

Sends results to the scoreboard for comparison.

B. Scoreboard (scoreboard.sv)
Compares the DUT output (from dest_mon) vs. reference model output.

Counts matches/mismatches and reports test status.

Fails if mismatches exceed a threshold.

6. Simulation Flow
Initialization:

top.sv starts the clock and UVM test.

test.sv configures the environment.

Stimulus Generation:

v_sequence triggers main_sequence to generate transactions.

src_drv applies them to the DUT.

Monitoring & Checking:

src_mon sends inputs to the reference model.

dest_mon captures DUT output (count).

Scoreboard compares DUT vs. reference results.

Result Reporting:

Prints matched/mismatched counts.

Reports PASS/FAIL based on mismatches.

7. Key Features of This Testbench
✔ Modular UVM Structure (Agents, Env, Tests).
✔ Randomized Stimulus Generation with constraints.
✔ Self-Checking Mechanism (Scoreboard + Reference Model).
✔ Configurable Agent Modes (Active/Passive).
✔ Virtual Sequencer for multi-agent coordination.
✔ Transaction-Level Modeling (TLM) for communication.
