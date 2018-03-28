# Fermion_Table

## Representation of Manybody-State

In the quantum cluster calculation in the Fermi system, we need to 'code' the manybody-state which is a direct product of single particle state. Assume for a system contains N site (the orbital information is included), the many body state can be represent as:

  |ѱ> =|φ<sub>1</sub>>|φ<sub>2</sub>>...|φ<sub>N</sub>> ⊗ |ζ<sub>1</sub>>|ζ<sub>2</sub>>...|ζ<sub>N</sub>>

  where |φ<sub>i</sub>> (|ζ<sub>i</sub>>) = 0 or 1  represent there is one particle of spin up(down) at site i. In this way we can represent a manybody state in a N site system by 2N bit. For example, for a system with N=3, a state

  |ѱ> =|101011> = |101>⊗|011> = |↑0↑>⊗|0↓↓>

  represent a state that: at site 1 there is a spin-up particle, at site 2 there is a spin down particle and at site 3 there is a spin-up and spin-down particle. We can further represent this state by a integer:

  |ѱ> =|101011> = |43> because 43 is the Decimal for of 101011. So we see that we can have a one-on-one map relation between a integer and a quantum state. For a system with size N, the physical state is ranging from |0> to |4<sup>N</sup>-1>. So the basis of this representation is {|i>}. When this representation is used, all the fermion operator(I also have this convenient code) can be represent at a action on this integers. So the Hamiltonian(H) becomes a d X d matrix , where d = 4<sup>N</sup>.
## Subspace
Basically d is a very large number so the diagonalization of H is difficult. For many cases, we can use the symmetry of H. For example, if we have [H,n] = 0 where n is the total particle operator, H will not change the particle number of the system. We can classify all the basis {|i>} into several categories.</br> &nbsp;
For example N=3 case. The basis {|i>,i=0,63} can be classified into different subspaces as: s<sub>0</sub>={|0>} , s<sub>1</sub>={|1>,|2>,|4>,|8>,...}, s<sub>2</sub>={|3>,|5>,|6>,|10>,...},...
In the binary form we can see that all the basis in s<sub>1</sub> contains 1 particle only and s<sub>2</sub> contains 2,... . In s<sub>0</sub>, there is only one state. Because [H,n] = 0 , for all the matrix element H<sub>ij</sub> where i and j are belong to different subspace, H<sub>ij</sub> = 0. In other words, H can be separated into different blocks. We need only to diagonalize each block.
## What this module do
As we have shown, we can simplify H when [H,n] = 0. In some system we can even have [H,S<sub>z</sub>]=0 where S<sub>z</sub> is the total spin (z component). This module create a table which then offer some method to check where a state is located in subspace and vice versa. In the module we define a variant called "symmetry":
* symmetry = 0 : do not use symetry
* symmetry = 1 : [H,n] = 0
* symmetry = 2 : [H,n] = 0 and [H,S<sub>z</sub>]=0

By using this module, it becomes very easy to change the case(symmetry) without changing other interface. When some "bad term" which breaks down the symmetry of H is added to H, we need not to range the basis again. One need only to decrease the value of symmetry.

## A simple example of usage:

    program main
      use fermion_table
      implicit none

      type(table)::t
      integer::m(2)
      integer*8::psi

      !   Initialization.   This example shows a system with 3 site and  symmetry = 2
      call t%Initialization(ns = 3 ,  symmetry = 2 )



      ! show how many subspace are there
      write(*,*)"The number of subspace is:" , t%get_nsub()

      ! show some information of subspace id = 5 for example
      ! The subspace is marked by ( nup =1 , ndown = 1 )
      write(*,*)t%get_subspace_marker(subid=6)




      ! We can use a integer::marker(2) to mark the different subspace
      ! if symmtry = 0 , there is only one subspace. So marker is useless
      ! if symmtry = 1 , marker(1) reperent the total particle marker(2) is not used.
      ! if symmtry = 2 , the two value of marker will represent number of particle that spin up and down respectivily.
      call t%get_sub_mark_value( subid = 6 , res = m )
       write(*,*)"the marker value of subspace id = 6:",  m


       ! For a working basis corresponding to the 4-th state in subid = 6
       psi = t%get_subindex_to_basis(subid=6,index=4)
       write(*,*)"The 4-th state in subspace6 is:",psi


       ! We then try to do the inverse thing. For a given basis psi = 17, we check it id in subspace
       write(*,*)"The subspace index is:",t%get_subid_from_basis(basis=17_8)
       ! This function, in fact, is not so usefull in practice. The most import infor is the index of
       ! of this basis in the subspace, which is shown below
       write(*,*)"The order of psi=17 in the subspace is: ",t%get_basis_to_sub_index(basis=17_8)


       ! some other usefull function can be found in source code directly.
       ! This module should contains all the requirments to built a higher level package.
       ! One should not confuse about that some functions can not be found in this module.
       ! If you really think so, the function you really want, in fact, is not needed at all!


    end
