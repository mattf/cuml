# =============================================================================
# Copyright (c) 2022, NVIDIA CORPORATION.
#
# Licensed under the Apache License, Version 2.0 (the "License"); you may not use this file except
# in compliance with the License. You may obtain a copy of the License at
#
# http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software distributed under the License
# is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express
# or implied. See the License for the specific language governing permissions and limitations under
# the License.
# =============================================================================


set(cython_sources
    ann.pyx
    kneighbors_classifier.pyx
    kneighbors_regressor.pyx
    nearest_neighbors.pyx
)

if(NOT SINGLEGPU)
  list(APPEND cython_sources
       kneighbors_classifier_mg.pyx
       kneighbors_regressor_mg.pyx
       nearest_neighbors_mg.pyx
  )
endif()

rapids_cython_create_modules(
  CXX
  SOURCE_FILES "${cython_sources}"
  LINKED_LIBRARIES $<IF:$<BOOL:${SINGLE_GPU}>,${cuml_sg_libraries},${cuml_mg_libraries}>
  MODULE_PREFIX neighbors_
  ASSOCIATED_TARGETS cuml
)

foreach(target IN LISTS targets_using_numpy)
  target_include_directories(${target} PRIVATE "${Python_NumPy_INCLUDE_DIRS}")
endforeach()
