# Copyright (c) 2018-2019, CNRS-UM LIRMM
# All rights reserved.
#
# Redistribution and use in source and binary forms, with or without
# modification, are permitted provided that the following conditions are met:
#
# 1. Redistributions of source code must retain the above copyright notice,
# this list of conditions and the following disclaimer.
#
# 2. Redistributions in binary form must reproduce the above copyright notice,
# this list of conditions and the following disclaimer in the documentation
# and/or other materials provided with the distribution.
#
# THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
# AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
# IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE
# ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE
# LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR
# CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF
# SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS
# INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN
# CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE)
# ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE
# POSSIBILITY OF SUCH DAMAGE.

set(CONTROLLER_SRC
    Controller.cpp
    FloatingBaseObserver.cpp
    FootstepPlan.cpp
    ModelPredictiveControl.cpp
    NetWrenchObserver.cpp
    Pendulum.cpp
    PlanInterpolator.cpp
    Preview.cpp
    Stabilizer.cpp
    SwingFoot.cpp)

set(CONTROLLER_HDR
    ${CMAKE_CURRENT_SOURCE_DIR}/../include/lipm_walking/Contact.h
    ${CMAKE_CURRENT_SOURCE_DIR}/../include/lipm_walking/Controller.h
    ${CMAKE_CURRENT_SOURCE_DIR}/../include/lipm_walking/FloatingBaseObserver.h
    ${CMAKE_CURRENT_SOURCE_DIR}/../include/lipm_walking/FootstepPlan.h
    ${CMAKE_CURRENT_SOURCE_DIR}/../include/lipm_walking/ModelPredictiveControl.h
    ${CMAKE_CURRENT_SOURCE_DIR}/../include/lipm_walking/NetWrenchObserver.h
    ${CMAKE_CURRENT_SOURCE_DIR}/../include/lipm_walking/Pendulum.h
    ${CMAKE_CURRENT_SOURCE_DIR}/../include/lipm_walking/PlanInterpolator.h
    ${CMAKE_CURRENT_SOURCE_DIR}/../include/lipm_walking/Preview.h
    ${CMAKE_CURRENT_SOURCE_DIR}/../include/lipm_walking/Sole.h
    ${CMAKE_CURRENT_SOURCE_DIR}/../include/lipm_walking/Stabilizer.h
    ${CMAKE_CURRENT_SOURCE_DIR}/../include/lipm_walking/State.h
    ${CMAKE_CURRENT_SOURCE_DIR}/../include/lipm_walking/SwingFoot.h
    ${CMAKE_CURRENT_SOURCE_DIR}/../include/lipm_walking/utils/ExponentialMovingAverage.h
    ${CMAKE_CURRENT_SOURCE_DIR}/../include/lipm_walking/utils/LeakyIntegrator.h
    ${CMAKE_CURRENT_SOURCE_DIR}/../include/lipm_walking/utils/LowPassVelocityFilter.h
    ${CMAKE_CURRENT_SOURCE_DIR}/../include/lipm_walking/utils/StationaryOffsetFilter.h
    ${CMAKE_CURRENT_SOURCE_DIR}/../include/lipm_walking/utils/clamp.h
    ${CMAKE_CURRENT_SOURCE_DIR}/../include/lipm_walking/utils/polynomials.h
    ${CMAKE_CURRENT_SOURCE_DIR}/../include/lipm_walking/utils/slerp.h
    ${CMAKE_CURRENT_SOURCE_DIR}/../include/lipm_walking/utils/world.h)

add_library(${PROJECT_NAME} SHARED ${CONTROLLER_SRC} ${CONTROLLER_HDR})
target_include_directories(${PROJECT_NAME} PUBLIC $<INSTALL_INTERFACE:include> $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/include>)
target_link_libraries(${PROJECT_NAME} PUBLIC copra::copra eigen-quadprog::eigen-quadprog mc_rtc::mc_control_fsm)
install(TARGETS ${PROJECT_NAME} DESTINATION "${MC_RTC_LIBDIR}")

add_controller(${PROJECT_NAME}_controller lib.cpp "")
set_target_properties(${PROJECT_NAME}_controller PROPERTIES OUTPUT_NAME "${PROJECT_NAME}")
target_link_libraries(${PROJECT_NAME}_controller PUBLIC ${PROJECT_NAME})

configure_file("${CMAKE_CURRENT_SOURCE_DIR}/../etc/LIPMWalking.conf.cmake" "${CMAKE_CURRENT_BINARY_DIR}/etc/LIPMWalking.conf")
install(FILES "${CMAKE_CURRENT_BINARY_DIR}/etc/LIPMWalking.conf" DESTINATION "${MC_RTC_LIBDIR}/mc_controller/etc/")

add_subdirectory(states)
