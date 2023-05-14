# CMSC818J-Project

## Introduction
This paper presents an optimized round-robin allocation strategy for reducing load imbalance. The proposed
approach utilizes a First-In-First-Out (FIFO) buffer along with
the existing hardware for round-robin allocation to allocate
new tasks to processing elements (PEs) immediately after they
complete their current task, reducing wait times for faster
PEs. The FIFO buffer also stores rows in sorted order based
on the number of non-zero elements, thereby enabling even
workload distribution over the PEs. Our simulations demonstrate
that the optimized solution offers lower latency and better PE
utilization compared to the baseline solution in most cases. While
the optimized solution performs similarly to the baseline in
worst-case scenarios, the proposed approach offers significant
improvements in most practical applications. 
## Set Up 
- Download the Baseline and Optimized architecture jupyter notebooks and run them 
- Use the Test directory to feed the input matrices 
## Simulator 
### Implementation  
- The simulator reads the input matrices from Data directory and stores the first matrix in csr and second matrix in csc format of python's library for sparse matrices
- The metrics are stored in `PE_stats` which keeps track of idle cycles, busy cycles and max and min number of non-zeroes per PE
- There is loop that iterates over different values for PE sizes and multiplies each of the matrices 
- For A x B multipliction, the `psmigr_3.mtx` from SuiteSparse is resized based on first matrix dimensions and then multiplied to it 
- For each column of the first matrix, all the rows of the second matrix are read and multiplied with it 
- `sort_computer_rows` unpacks the csr format and reads the rows based on the reading rate defined 
    - For baseline solution, the rows are stored in a list 
    - For optimized solution. the rows are sorted based on number of non-zero elements and then added to the FIFO buffer 
- After the rows are read, the simulator assigns the rows to the PEs and `PE_mult` is used to simulate PE MAC operations
- Once the PEs finishes the assigned task, the `PE_stats` metrics are updated along with the variable keeping track of the total number of cycles
    - The `PE_mult` function updates the busy cycles metric 
    - For baseline solution, `idle_cycle_list` keeps track of number of busy cycles per allocation for 
      each PE. At the end of each allocation, the PE with the max busy cycle is used to calculate idle cycles for other PEs. The `add_idle_cycles2` computes the number of cycles 
      each PE has to wait for the slowest PE to finish computation. The `accum_cycle_list` keeps track of the number of idle cycles for each PE  while the output is being written back to output buffer.    
   - For optimized solution, the max total number of cycles are tracked and once all the allocations are finished, the idle cycles are added. The PE that had the longest workload 
     is used for calculating idle cycles for other PEs. The `add_idle_cycles2` computes the number of cycles each PE has to wait for the slowest PE to finish computation at the last allocation. 
 - Once the output is accumulated, it is validated for correctness using the `np.mult` function of numpy library in python. 
 - The PE_metrics are then added to the output files. The output files can be found in the Data directory. 

**For more details, please check the comments in Baseline and Optimized Architecture file**

### Data Analysis 
- The data collected from the simulator is analyzed in the Data Analysis file. The final graphs outputs are also available there. 
 
